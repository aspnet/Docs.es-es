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
ms.openlocfilehash: e476f3f62a250d960c809e5062b3bd8ca3a1940a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587245"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="178ac-103">Interfaz web abierta para .NET (OWIN) con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="178ac-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="178ac-104">Por [Steve Smith](https://ardalis.com/) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="178ac-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="178ac-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="178ac-105">ASP.NET Core:</span></span>

* <span data-ttu-id="178ac-106">Es compatible con la interfaz web abierta para .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="178ac-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="178ac-107">Tiene reemplazos compatibles con .NET Core para las bibliotecas `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).</span><span class="sxs-lookup"><span data-stu-id="178ac-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="178ac-108">OWIN permite que las aplicaciones web se desacoplen de los servidores web.</span><span class="sxs-lookup"><span data-stu-id="178ac-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="178ac-109">Define una manera estándar para usar software intermedio en una canalización a fin de controlar las solicitudes y las respuestas asociadas.</span><span class="sxs-lookup"><span data-stu-id="178ac-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="178ac-110">El software intermedio y las aplicaciones de ASP.NET Core pueden interoperar con aplicaciones, servidores y software intermedio basados en OWIN.</span><span class="sxs-lookup"><span data-stu-id="178ac-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="178ac-111">OWIN proporciona una capa de desacoplamiento que permite que dos marcos de trabajo con modelos de objetos dispares se usen juntos.</span><span class="sxs-lookup"><span data-stu-id="178ac-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="178ac-112">El paquete `Microsoft.AspNetCore.Owin` proporciona dos implementaciones del adaptador:</span><span class="sxs-lookup"><span data-stu-id="178ac-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="178ac-113">De ASP.NET Core a OWIN</span><span class="sxs-lookup"><span data-stu-id="178ac-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="178ac-114">De OWIN a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="178ac-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="178ac-115">Esto permite que ASP.NET Core se hospede sobre un servidor/host compatible con OWIN, o bien que otros componentes compatibles con OWIN se ejecuten sobre ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="178ac-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="178ac-116">El uso de estos adaptadores conlleva un costo de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="178ac-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="178ac-117">Las aplicaciones que solo usan componentes de ASP.NET Core no deben usar el paquete o adaptadores de `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="178ac-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="178ac-118">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="178ac-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="178ac-119">Ejecución de software intermedio de OWIN en la canalización de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="178ac-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="178ac-120">La compatibilidad con OWIN de ASP.NET Core se implementa como parte del paquete `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="178ac-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="178ac-121">Puede importar compatibilidad con OWIN en el proyecto mediante la instalación de este paquete.</span><span class="sxs-lookup"><span data-stu-id="178ac-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="178ac-122">El software intermedio de OWIN cumple la [especificación de OWIN](https://owin.org/spec/spec/owin-1.0.0.html), que requiere una interfaz `Func<IDictionary<string, object>, Task>` y el establecimiento de determinadas claves (como `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="178ac-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="178ac-123">En el siguiente software intermedio simple de OWIN se muestra "Hello World":</span><span class="sxs-lookup"><span data-stu-id="178ac-123">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="178ac-124">La firma de ejemplo devuelve un valor `Task` y acepta un valor `IDictionary<string, object>`, según los requisitos de OWIN.</span><span class="sxs-lookup"><span data-stu-id="178ac-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="178ac-125">En el código siguiente se muestra cómo agregar el software intermedio `OwinHello` (mostrado arriba) a la canalización ASP.NET Core con el método de extensión `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="178ac-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="178ac-126">Puede configurar la realización de otras acciones en la canalización de OWIN.</span><span class="sxs-lookup"><span data-stu-id="178ac-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="178ac-127">Los encabezados de respuesta solo deben modificarse antes de la primera escritura en la secuencia de respuesta.</span><span class="sxs-lookup"><span data-stu-id="178ac-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="178ac-128">No se recomienda la realización de varias llamadas a `UseOwin` por motivos de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="178ac-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="178ac-129">Los componentes de OWIN funcionarán mejor si se agrupan.</span><span class="sxs-lookup"><span data-stu-id="178ac-129">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="178ac-130">Ejecución de ASP.NET Core en un servidor basado en OWIN y uso de su compatibilidad con WebSockets</span><span class="sxs-lookup"><span data-stu-id="178ac-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="178ac-131">Otro ejemplo de la manera en que ASP.NET Core puede aprovechar las características de los servidores basados en OWIN es el acceso a funciones como WebSockets.</span><span class="sxs-lookup"><span data-stu-id="178ac-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="178ac-132">El servidor web de OWIN de .NET usado en el ejemplo anterior es compatible con WebSockets integrado, cuyas ventajas puede aprovechar la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="178ac-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="178ac-133">En el ejemplo siguiente se muestra una aplicación web simple que admite WebSockets y devuelve todo lo que se envía al servidor a través de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="178ac-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="178ac-134">Entorno de OWIN</span><span class="sxs-lookup"><span data-stu-id="178ac-134">OWIN environment</span></span>

<span data-ttu-id="178ac-135">Puede construir un entorno de OWIN por medio de `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="178ac-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="178ac-136">Claves de OWIN</span><span class="sxs-lookup"><span data-stu-id="178ac-136">OWIN keys</span></span>

<span data-ttu-id="178ac-137">OWIN depende de un objeto `IDictionary<string,object>` para comunicar información mediante un intercambio de solicitud/respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="178ac-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="178ac-138">ASP.NET Core implementa las claves que se enumeran a continuación.</span><span class="sxs-lookup"><span data-stu-id="178ac-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="178ac-139">Vea las [extensiones de la especificación principal](https://owin.org/#spec) y las [directrices principales y claves comunes de OWIN](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="178ac-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="178ac-140">Datos de solicitud (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="178ac-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="178ac-141">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-141">Key</span></span>               | <span data-ttu-id="178ac-142">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-142">Value (type)</span></span> | <span data-ttu-id="178ac-143">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="178ac-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="178ac-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="178ac-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="178ac-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="178ac-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="178ac-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="178ac-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="178ac-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="178ac-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="178ac-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="178ac-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="178ac-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="178ac-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="178ac-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="178ac-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="178ac-152">Datos de solicitud (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="178ac-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="178ac-153">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-153">Key</span></span>               | <span data-ttu-id="178ac-154">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-154">Value (type)</span></span> | <span data-ttu-id="178ac-155">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="178ac-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="178ac-157">Optional</span><span class="sxs-lookup"><span data-stu-id="178ac-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="178ac-158">Datos de respuesta (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="178ac-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="178ac-159">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-159">Key</span></span>               | <span data-ttu-id="178ac-160">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-160">Value (type)</span></span> | <span data-ttu-id="178ac-161">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="178ac-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="178ac-163">Optional</span><span class="sxs-lookup"><span data-stu-id="178ac-163">Optional</span></span> |
| <span data-ttu-id="178ac-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="178ac-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="178ac-165">Optional</span><span class="sxs-lookup"><span data-stu-id="178ac-165">Optional</span></span> |
| <span data-ttu-id="178ac-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="178ac-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="178ac-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="178ac-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="178ac-168">Otros datos (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="178ac-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="178ac-169">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-169">Key</span></span>               | <span data-ttu-id="178ac-170">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-170">Value (type)</span></span> | <span data-ttu-id="178ac-171">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="178ac-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="178ac-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="178ac-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="178ac-174">Claves comunes</span><span class="sxs-lookup"><span data-stu-id="178ac-174">Common keys</span></span>

| <span data-ttu-id="178ac-175">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-175">Key</span></span>               | <span data-ttu-id="178ac-176">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-176">Value (type)</span></span> | <span data-ttu-id="178ac-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="178ac-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="178ac-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="178ac-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="178ac-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="178ac-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="178ac-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="178ac-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="178ac-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="178ac-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="178ac-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="178ac-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="178ac-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="178ac-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="178ac-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="178ac-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="178ac-186">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="178ac-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="178ac-187">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-187">Key</span></span>               | <span data-ttu-id="178ac-188">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-188">Value (type)</span></span> | <span data-ttu-id="178ac-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="178ac-190">sendfile.SendAsync</span></span> | <span data-ttu-id="178ac-191">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="178ac-192">Por solicitud</span><span class="sxs-lookup"><span data-stu-id="178ac-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="178ac-193">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="178ac-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="178ac-194">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-194">Key</span></span>               | <span data-ttu-id="178ac-195">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-195">Value (type)</span></span> | <span data-ttu-id="178ac-196">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="178ac-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="178ac-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="178ac-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="178ac-199">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="178ac-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="178ac-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="178ac-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="178ac-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="178ac-202">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="178ac-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="178ac-203">Key</span><span class="sxs-lookup"><span data-stu-id="178ac-203">Key</span></span>               | <span data-ttu-id="178ac-204">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="178ac-204">Value (type)</span></span> | <span data-ttu-id="178ac-205">Descripción</span><span class="sxs-lookup"><span data-stu-id="178ac-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="178ac-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="178ac-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="178ac-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="178ac-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="178ac-208">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="178ac-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="178ac-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="178ac-210">Sin especificaciones</span><span class="sxs-lookup"><span data-stu-id="178ac-210">Non-spec</span></span> |
| <span data-ttu-id="178ac-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="178ac-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="178ac-212">Vea la [sección 4.2.2 de RFC6455](https://tools.ietf.org/html/rfc6455#section-4.2.2), paso 5.5</span><span class="sxs-lookup"><span data-stu-id="178ac-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="178ac-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="178ac-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="178ac-214">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="178ac-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="178ac-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="178ac-216">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="178ac-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="178ac-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="178ac-218">Vea [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Signatura de delegado)</span><span class="sxs-lookup"><span data-stu-id="178ac-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="178ac-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="178ac-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="178ac-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="178ac-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="178ac-221">Optional</span><span class="sxs-lookup"><span data-stu-id="178ac-221">Optional</span></span> |
| <span data-ttu-id="178ac-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="178ac-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="178ac-223">Optional</span><span class="sxs-lookup"><span data-stu-id="178ac-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="178ac-224">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="178ac-224">Additional resources</span></span>

* [<span data-ttu-id="178ac-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="178ac-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="178ac-226">Servidores</span><span class="sxs-lookup"><span data-stu-id="178ac-226">Servers</span></span>](xref:fundamentals/servers/index)
