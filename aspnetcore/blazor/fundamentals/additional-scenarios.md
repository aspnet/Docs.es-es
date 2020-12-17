---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre más escenarios de configuración del modelo de hospedaje de Blazor en ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485880"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>Configuración del modelo de hospedaje de Blazor en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) y [Luke Latham](https://github.com/guardrex)

En este artículo se describe la configuración del modelo de hospedaje.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>Negociación entre orígenes de SignalR para la autenticación

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

## <a name="initialize-the-no-locblazor-circuit"></a>Inicialización del circuito de Blazor

*Esta sección es aplicable a Blazor Server.*

Configure el inicio manual de un [circuito de SignalR](xref:blazor/hosting-models#circuits) de la aplicación Blazor Server en el archivo `Pages/_Host.cshtml`:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Coloque un script que llame a `Blazor.start` después de la etiqueta del script `blazor.server.js` y dentro de la etiqueta de cierre `</body>`.

Cuando `autostart` está deshabilitado, cualquier aspecto de la aplicación que no depende del circuito funciona normalmente. Por ejemplo, el enrutamiento del lado cliente está operativo. Sin embargo, cualquier aspecto que dependa del circuito no funcionará hasta que se llame a `Blazor.start`. El comportamiento de la aplicación es imprevisible sin ningún circuito establecido. Por ejemplo, los métodos de componente no se ejecutan mientras el circuito está desconectado.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Inicialización de Blazor cuando el documento está listo

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

### <a name="configure-the-no-locsignalr-client"></a>Configuración del cliente de SignalR

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

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Desconexión del circuito Blazor del cliente

De forma predeterminada, un circuito Blazor se desconecta cuando se desencadena el [evento de página `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event). A fin de desconectar el circuito para otros escenarios en el cliente, invoque `Blazor.disconnect` en el controlador de eventos adecuado. En el ejemplo siguiente, el circuito se desconecta cuando la página está oculta ([evento `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

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

## <a name="static-files"></a>Archivos estáticos

*Esta sección es aplicable a Blazor Server.*

Para crear asignaciones de archivos adicionales con un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurar otros valores <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **uno** de los enfoques siguientes. En los ejemplos siguientes, el marcador de posición `{EXTENSION}` es la extensión de archivo y `{CONTENT TYPE}` es el tipo de contenido.

* Configure las opciones a través de la [inserción de dependencias (DI)](xref:blazor/fundamentals/dependency-injection) en `Startup.ConfigureServices` (`Startup.cs`) mediante <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Como este enfoque configura el mismo proveedor de archivos que se ha usado para proporcionar `blazor.server.js`, asegúrese de que la configuración personalizada no interfiera con la acción de proporcionar `blazor.server.js`. Por ejemplo, no quite la asignación para los archivos JavaScript mediante la configuración del proveedor con `provider.Mappings.Remove(".js")`.

* Use dos llamadas a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure` (`Startup.cs`):
  * Configure el proveedor de archivos personalizado en la primera llamada con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.
  * El segundo middleware proporciona `blazor.server.js`, que usa la configuración predeterminada de los archivos estáticos proporcionada por el marco Blazor.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Puede evitar interferir con el servicio de `_framework/blazor.server.js` mediante <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> para ejecutar un middleware de archivos estáticos personalizado:

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
* [Eventos de reconexión y de ciclo de vida de componentes de Blazor Server](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
