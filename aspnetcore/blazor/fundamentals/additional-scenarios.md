---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre más escenarios de configuración del modelo de hospedaje de Blazor en ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
ms.openlocfilehash: 870509a3cbbcbea9b1c4804185c49a831af22630
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009640"
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

*Esta sección es aplicable a Blazor Server.*

Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él. Esto se configura en la página `_Host.cshtml` Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:

* Se representa previamente en la página.
* Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

| Modo de representación | Descripción |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server. La salida del componente no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente en HTML estático. |

No se admite la representación de componentes de servidor desde una página HTML estática.

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
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
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

::: moniker-end

## <a name="influence-html-head-tag-elements"></a>Influencia en los elementos de etiqueta `<head>`

*Esta sección se aplica a la próxima versión de ASP.NET Core 5.0 de Blazor WebAssembly y Blazor Server.*

Cuando se representan, los componentes `Title`, `Link` y `Meta` agregan o actualizan los datos en los elementos de etiqueta `<head>` HTML:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

En el ejemplo anterior, los marcadores de posición de `{TITLE}`, `{URL}` y `{DESCRIPTION}` son valores de cadena, variables de Razor o expresiones de Razor.

Se aplican las siguientes características:

* Se admite la representación previa del lado servidor.
* El parámetro `Value` es el único parámetro válido para el componente `Title`.
* Los atributos HTML proporcionados a los componentes `Meta` y `Link` se capturan en [atributos adicionales](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) y se pasan a la etiqueta HTML representada.
* Si se trata de varios componentes `Title`, el título de la página refleja el elemento `Value` del último componente `Title` representado.
* Si se incluyen varios componentes `Meta` o `Link` con atributos idénticos, hay exactamente una etiqueta HTML representada por componente `Meta` o `Link`. Dos componentes `Meta` o `Link` no pueden hacer referencia a la misma etiqueta HTML representada.
* Los cambios en los parámetros de los componentes `Meta` o `Link` existentes se reflejan en sus etiquetas HTML representadas.
* Cuando los componentes `Link` o `Meta` ya no se representan y, por lo tanto, el marco de trabajo los elimina, se quitan sus etiquetas HTML representadas.

Cuando se usa uno de los componentes de .NET Framework en un componente secundario, la etiqueta HTML representada influye en cualquier otro componente secundario del componente primario, siempre que se represente el componente secundario que contiene el componente del marco de trabajo. La distinción entre el uso de uno de estos componentes del marco de trabajo en un componente secundario y la colocación de una etiqueta HTML en `wwwroot/index.html` o `Pages/_Host.cshtml` es que la etiqueta HTML representada de un componente del marco de trabajo:

* Se puede modificar según el estado de la aplicación. No se puede modificar una etiqueta HTML codificada de forma rígida según el estado de la aplicación.
* Se quita del elemento HTML `<head>` cuando ya no se representa el componente primario.

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

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
