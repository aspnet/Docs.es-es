---
title: Configuración de SignalR en ASP.NET Core
author: bradygaster
description: Aprenda a configurar ASP.NET Core SignalR aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: d451e8807d761ab11509d33951009a98845f7e5e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021866"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a>Configuración de SignalR en ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` . El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos. Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).

Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Cambiar a Newtonsoft.Jsactivado

Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble del `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva. Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Vea a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Vea a continuación. | Opciones adicionales específicas del transporte de WebSockets. |
| `MinimumProtocolVersion` | 0 | Especifique la versión mínima del protocolo Negotiate. Se utiliza para limitar a los clientes a las versiones más recientes. |

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configuración de opciones de cliente

Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.

### <a name="configure-logging"></a>registro

El registro se configura en el cliente .NET mediante el `ConfigureLogging` método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, `configureLogging` existe un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro. Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará. Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o bien**`information` | `LogLevel.Information` |
| `warn`**o bien**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.

Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript). Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión del cliente de Java, WebSockets es el único transporte disponible.

En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` . De forma predeterminada, el cliente de Java usa el transporte de WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El SignalR cliente de Java no admite todavía la reserva de transporte.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .

En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `ClientCertificates` | Vacío | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Vacío | Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `headers` | `null` | Diccionario de encabezados enviados con cada solicitud HTTP. El envío de encabezados en el explorador no funciona con WebSockets ni con el <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flujo. |
| `logMessageContent` | `null` | Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withCredentials` | `true` | Especifica si las credenciales se enviarán con la solicitud de CORS. Azure App Service usa cookie para las sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente. Para obtener más información sobre CORS con SignalR , vea <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withHeader` `withHeaders` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` . El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos. Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).

Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Cambiar a Newtonsoft.Jsactivado

Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble del `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva. Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Vea a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Vea a continuación. | Opciones adicionales específicas del transporte de WebSockets. |
| `MinimumProtocolVersion` | 0 | Especifique la versión mínima del protocolo Negotiate. Se utiliza para limitar a los clientes a las versiones más recientes. |

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configuración de opciones de cliente

Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.

### <a name="configure-logging"></a>registro

El registro se configura en el cliente .NET mediante el `ConfigureLogging` método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, `configureLogging` existe un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro. Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará. Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o bien**`information` | `LogLevel.Information` |
| `warn`**o bien**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.

Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript). Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión del cliente de Java, WebSockets es el único transporte disponible.

En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` . De forma predeterminada, el cliente de Java usa el transporte de WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El SignalR cliente de Java no admite todavía la reserva de transporte.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .

En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `ClientCertificates` | Vacío | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Vacío | Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `logMessageContent` | `null` | Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withHeader` `withHeaders` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`se puede agregar después [de SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` . El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos. Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).

Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Cambiar a Newtonsoft.Jsactivado

Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble del `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva. Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Vea a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Vea a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configuración de opciones de cliente

Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.

### <a name="configure-logging"></a>registro

El registro se configura en el cliente .NET mediante el `ConfigureLogging` método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, `configureLogging` existe un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro. Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará. Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o bien**`information` | `LogLevel.Information` |
| `warn`**o bien**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.

Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript). Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión del cliente de Java, WebSockets es el único transporte disponible.

En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` . De forma predeterminada, el cliente de Java usa el transporte de WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El SignalR cliente de Java no admite todavía la reserva de transporte.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .

En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `ClientCertificates` | Vacío | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Vacío | Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `logMessageContent` | `null` | Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withHeader` `withHeaders` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método. El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos. Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo. Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa. El valor recomendado es el doble del `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial. |

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer. Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer. Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Vea a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Vea a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configuración de opciones de cliente

Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.

### <a name="configure-logging"></a>registro

El registro se configura en el cliente .NET mediante el `ConfigureLogging` método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, `configureLogging` existe un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.

Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript). Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión del cliente de Java, WebSockets es el único transporte disponible.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .

En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes de ping. Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `ClientCertificates` | Vacío | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Vacío | Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `logMessageContent` | `null` | Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withHeader` `withHeaders` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización de JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [ SignalR Agregar](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método. El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto. La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos. Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.

## <a name="configure-server-options"></a>Configurar opciones de servidor

En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión. Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este centro. De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales. |
| `EnableDetailedErrors` | `false` | Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador. El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial. |

Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones de configuración de HTTP avanzadas

Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Número máximo de bytes recibidos del cliente que el servidor almacena en búfer. Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `AuthorizationData` | Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub. | Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro. |
| `TransportMaxBufferSize` | 32 KB | Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer. Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse. |
| `LongPolling` | Vea a continuación. | Opciones adicionales específicas del transporte de sondeo prolongado. |
| `WebSockets` | Vea a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo. Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia. |

El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configuración de opciones de cliente

Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript). También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.

### <a name="configure-logging"></a>registro

El registro se configura en el cliente .NET mediante el `ConfigureLogging` método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet. Llame al `AddConsole` método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente de JavaScript, `configureLogging` existe un método similar. Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.

Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).

El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede omitir sin ningún riesgo.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript). Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurar la autenticación de portador

Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ). En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets). En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .

En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar las opciones de tiempo de espera y mantenimiento de conexión

En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript). Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript). Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento. Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera. El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el protocolo de enlace inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento. Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción de .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `ClientCertificates` | Vacío | Colección de certificados TLS que se enviarán a las solicitudes de autenticación. |
| `Cookies` | Vacío | Colección de HTTP cookie s que se va a enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales que se van a enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Solo WebSockets. Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP. No se usa para las conexiones WebSocket. Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como, por ejemplo, los Cookie encabezados y) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Proxy HTTP que se va a usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto habilita el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones adicionales de WebSocket. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción de JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `logMessageContent` | `null` | Establezca en `true` para registrar los bytes/caracteres de los mensajes enviados y recibidos por el cliente. |
| `skipNegotiation` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opción de Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establézcalo en `true` para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**. Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR . |
| `withHeader` `withHeaders` | Vacío | Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP. |

---

En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
