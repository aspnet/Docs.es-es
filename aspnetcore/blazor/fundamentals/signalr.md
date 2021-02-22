---
title: Guía de SignalR de Blazor para ASP.NET Core
author: guardrex
description: Aprenda a configurar y administrar conexiones de SignalR de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279867"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a>Guía de SignalR de Blazor para ASP.NET Core

Para obtener instrucciones generales sobre la configuración de SignalR para ASP.NET Core, consulte los temas del área <xref:signalr/introduction> de la documentación. Para configurar SignalR [agregado a una solución de Blazor WebAssembly hospedada](xref:tutorials/signalr-blazor), vea <xref:signalr/configuration#configure-server-options>.

## <a name="circuit-handler-options"></a>Opciones de controlador de circuito

*Esta sección es aplicable a Blazor Server.*

Configure el circuito Blazor Server con los valores de <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> que se muestran en la tabla siguiente.

| Opción | Valor predeterminado | Descripción |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | Envío de mensajes de excepción detallados a JavaScript cuando se produce una excepción no controlada en el circuito o cuando una invocación de método de .NET a través de la interoperabilidad de JS produce una excepción. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | Número máximo de circuitos desconectados que contiene a la vez un servidor determinado en la memoria. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 minutos | Cantidad máxima de tiempo que un circuito desconectado se conserva en la memoria antes de desactivarlo. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 minuto | Cantidad máxima de tiempo que el servidor aguarda antes de agotar el tiempo de espera de invocación de una función de JavaScript asincrónica. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | Número máximo de lotes de representación no confirmados por circuito que el servidor mantiene en la memoria en un momento dado para admitir una reconexión sólida. Después de alcanzar el límite, el servidor deja de generar nuevos lotes de representación hasta que el cliente confirma uno o varios lotes. |

Configure las opciones de `Startup.ConfigureServices` con un delegado de opciones en <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. En el ejemplo siguiente se asignan los valores de opción predeterminados que se muestran en la tabla anterior:

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

Para configurar <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> con <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>. Para obtener descripciones de las opciones, vea <xref:signalr/configuration#configure-server-options>. En el ejemplo siguiente se asignan los valores de opción predeterminados:

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>Negociación entre orígenes de SignalR para la autenticación

*Esta sección es aplicable a Blazor WebAssembly.*

Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:

* Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Reflejo del estado de la conexión en la interfaz de usuario

*Esta sección es aplicable a Blazor Server.*

Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse. Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.

Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Agregue lo siguiente a la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.

| Clase de CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Una conexión perdida. El cliente intenta volver a conectarse. Se muestra el modal. |
| `components-reconnect-hide`     | Se restablece una conexión activa con el servidor. Se oculta el modal. |
| `components-reconnect-failed`   | Error de reconexión, probablemente debido a un error de la red. Para intentar la reconexión, llame a `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Reconexión rechazada. Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor. Para volver a cargar la aplicación, llame a `location.reload()`. Este estado de conexión se puede producir cuando:<ul><li>Se produce un bloqueo en el circuito del lado servidor.</li><li>El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario. Se eliminan las instancias de los componentes con las que el usuario interactúa.</li><li>El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</li></ul> |

## <a name="render-mode"></a>Modo de representación

::: moniker range=">= aspnetcore-5.0"

*Esta sección se aplica a Blazor WebAssembly y Blazor Server hospedados.*

Las aplicaciones de Blazor se configuran de forma predeterminada para representar previamente la interfaz de usuario en el servidor. Para más información, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Esta sección es aplicable a Blazor Server.*

Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él. Para más información, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a>Inicialización del circuito de Blazor

*Esta sección es aplicable a Blazor Server.*

Configure el inicio manual de un [circuito de SignalR](xref:blazor/hosting-models#circuits) de la aplicación Blazor Server en el archivo `Pages/_Host.cshtml`:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Coloque un script que llame a `Blazor.start` después de la etiqueta del script `blazor.server.js` y dentro de la etiqueta de cierre `</body>`.

Cuando `autostart` está deshabilitado, cualquier aspecto de la aplicación que no depende del circuito funciona normalmente. Por ejemplo, el enrutamiento del lado cliente está operativo. Sin embargo, cualquier aspecto que dependa del circuito no funcionará hasta que se llame a `Blazor.start`. El comportamiento de la aplicación es imprevisible sin ningún circuito establecido. Por ejemplo, los métodos de componente no se ejecutan mientras el circuito está desconectado.

### <a name="initialize-blazor-when-the-document-is-ready"></a>Inicialización de Blazor cuando el documento está listo

Para inicializar la aplicación Blazor cuando el documento está listo:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Cadena al elemento `Promise` resultante de un inicio manual

Para realizar tareas adicionales, como la inicialización de interoperabilidad de JS, use `then` como cadena al elemento `Promise` resultante de un inicio manual de la aplicación Blazor:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a>Configuración del cliente de SignalR

#### <a name="logging"></a>Registro

Para configurar el registro del cliente SignalR, pase un objeto de configuración (`configureSignalR`) que llame a `configureLogging` con el nivel de registro en el generador de cliente:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

En el ejemplo anterior, `information` es equivalente a un nivel de registro de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.

### <a name="modify-the-reconnection-handler"></a>Modificación del controlador de reconexión

Los eventos de conexión del circuito del controlador de reconexión pueden modificarse para obtener comportamientos personalizados, por ejemplo:

* Para notificar al usuario si la conexión se ha quitado.
* Para realizar el registro (desde el cliente) cuando un circuito está conectado.

Para modificar los eventos de conexión, registre las devoluciones de llamada para los siguientes cambios de conexión:

* Las conexiones que se quitan usan `onConnectionDown`.
* Las conexiones establecidas o restablecidas usan `onConnectionUp`.

Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajustar el número y el intervalo de reintentos de reconexión

Para ajustar el intervalo y el número de reintentos de reconexión, establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>Ocultar o reemplazar la pantalla de reconexión

Para ocultar la presentación de reconexión, establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.

::: moniker range=">= aspnetcore-5.0"

Personalice el retraso antes de que aparezca la pantalla de reconexión. Para ello, establezca la propiedad `transition-delay` en el CSS (`wwwroot/css/site.css`) de la aplicación como elemento modal. En el ejemplo siguiente se establece el retraso de transición entre 500 ms (valor predeterminado) y 1000 ms (1 segundo):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>Desconexión del circuito Blazor del cliente

De forma predeterminada, un circuito Blazor se desconecta cuando se desencadena el [evento de página `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event). A fin de desconectar el circuito para otros escenarios en el cliente, invoque `Blazor.disconnect` en el controlador de eventos adecuado. En el ejemplo siguiente, el circuito se desconecta cuando la página está oculta ([evento `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Eventos de reconexión y de ciclo de vida de componentes de Blazor Server](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
