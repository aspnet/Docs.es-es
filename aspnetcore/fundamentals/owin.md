---
title: Interfaz web abierta para .NET (OWIN) con ASP.NET Core
author: ardalis
description: Descubra la manera en que ASP.NET Core es compatible con la interfaz web abierta para .NET (OWIN), que permite que las aplicaciones web se desacoplen de servidores web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975279"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Interfaz web abierta para .NET (OWIN) con ASP.NET Core

Por [Steve Smith](https://ardalis.com/) y [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core:

* Es compatible con la interfaz web abierta para .NET (OWIN).
* Tiene reemplazos compatibles con .NET Core para las bibliotecas `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).

OWIN permite que las aplicaciones web se desacoplen de los servidores web. Define una manera estándar para usar software intermedio en una canalización a fin de controlar las solicitudes y las respuestas asociadas. El software intermedio y las aplicaciones de ASP.NET Core pueden interoperar con aplicaciones, servidores y software intermedio basados en OWIN.

OWIN proporciona una capa de desacoplamiento que permite que dos marcos de trabajo con modelos de objetos dispares se usen juntos. El paquete `Microsoft.AspNetCore.Owin` proporciona dos implementaciones del adaptador:

* De ASP.NET Core a OWIN 
* De OWIN a ASP.NET Core

Esto permite que ASP.NET Core se hospede sobre un servidor/host compatible con OWIN, o bien que otros componentes compatibles con OWIN se ejecuten sobre ASP.NET Core.

> [!NOTE]
> El uso de estos adaptadores conlleva un costo de rendimiento. Las aplicaciones que solo usan componentes de ASP.NET Core no deben usar el paquete o adaptadores de `Microsoft.AspNetCore.Owin`.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Ejecución de software intermedio de OWIN en la canalización de ASP.NET Core

La compatibilidad con OWIN de ASP.NET Core se implementa como parte del paquete `Microsoft.AspNetCore.Owin`. Puede importar compatibilidad con OWIN en el proyecto mediante la instalación de este paquete.

El software intermedio de OWIN cumple la [especificación de OWIN](https://owin.org/spec/spec/owin-1.0.0.html), que requiere una interfaz `Func<IDictionary<string, object>, Task>` y el establecimiento de determinadas claves (como `owin.ResponseBody`). En el siguiente software intermedio simple de OWIN se muestra "Hello World":

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

La firma de ejemplo devuelve un valor `Task` y acepta un valor `IDictionary<string, object>`, según los requisitos de OWIN.

En el código siguiente se muestra cómo agregar el software intermedio `OwinHello` (mostrado arriba) a la canalización ASP.NET Core con el método de extensión `UseOwin`.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Puede configurar la realización de otras acciones en la canalización de OWIN.

> [!NOTE]
> Los encabezados de respuesta solo deben modificarse antes de la primera escritura en la secuencia de respuesta.

> [!NOTE]
> No se recomienda la realización de varias llamadas a `UseOwin` por motivos de rendimiento. Los componentes de OWIN funcionarán mejor si se agrupan.

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Ejecución de ASP.NET Core en un servidor basado en OWIN y uso de su compatibilidad con WebSockets

Otro ejemplo de la manera en que ASP.NET Core puede aprovechar las características de los servidores basados en OWIN es el acceso a funciones como WebSockets. El servidor web de OWIN de .NET usado en el ejemplo anterior es compatible con WebSockets integrado, cuyas ventajas puede aprovechar la aplicación ASP.NET Core. En el ejemplo siguiente se muestra una aplicación web simple que admite WebSockets y devuelve todo lo que se envía al servidor a través de WebSockets.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a>Entorno de OWIN

Puede construir un entorno de OWIN por medio de `HttpContext`.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>Claves de OWIN

OWIN depende de un objeto `IDictionary<string,object>` para comunicar información mediante un intercambio de solicitud/respuesta HTTP. ASP.NET Core implementa las claves que se enumeran a continuación. Vea las [extensiones de la especificación principal](https://owin.org/#spec) y las [directrices principales y claves comunes de OWIN](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Datos de solicitud (OWIN v1.0.0)

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Datos de solicitud (OWIN v1.1.0)

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | Optional |

### <a name="response-data-owin-v100"></a>Datos de respuesta (OWIN v1.0.0)

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | Optional |
| owin.ResponseReasonPhrase | `String` | Optional |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Otros datos (OWIN v1.0.0)

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>Claves comunes

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado) | Por solicitud |

### <a name="opaque-v030"></a>Opaque v0.3.0

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Key               | Valor (tipo) | Descripción |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado) |
| websocket.AcceptAlt |  | Sin especificaciones |
| websocket.SubProtocol | `String` | Vea la [sección 4.2.2 de RFC6455](https://tools.ietf.org/html/rfc6455#section-4.2.2), paso 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | Optional |
| websocket.ClientCloseDescription | `String` | Optional |

## <a name="additional-resources"></a>Recursos adicionales

* [Middleware](xref:fundamentals/middleware/index)
* [Servidores](xref:fundamentals/servers/index)
