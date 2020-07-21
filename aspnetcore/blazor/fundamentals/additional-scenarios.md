---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre más escenarios de configuración del modelo de hospedaje de Blazor en ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239639"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Configuración del modelo de hospedaje de Blazor en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

En este artículo se describe la configuración del modelo de hospedaje.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>Negociación entre orígenes de SignalR para la autenticación

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

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configuración del cliente de SignalR para aplicaciones Blazor Server

*Esta sección es aplicable a Blazor Server.*

Configure el cliente SignalR que usan las aplicaciones Blazor Server en el archivo `Pages/_Host.cshtml`. Coloque un script que llame a `Blazor.start` después del script `_framework/blazor.server.js` y dentro de la etiqueta `</body>`.

### <a name="logging"></a>Registro

Para configurar el registro de cliente de SignalR:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Pase un objeto de configuración (`configureSignalR`) que llame a `configureLogging` con el nivel de registro en el generador de cliente.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

Para modificar los eventos de conexión:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Registre las devoluciones de llamada de los cambios de conexión para las conexiones quitadas (`onConnectionDown`) y las conexiones establecidas/restablecidas (`onConnectionUp`). Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

Para ajustar el número y el intervalo de reintentos de reconexión, siga estos pasos:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="hide-or-replace-the-reconnection-display"></a>Ocultar o reemplazar la pantalla de reconexión

Para ocultar la pantalla de reconexión:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
