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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="693d9-103">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="693d9-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="693d9-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="693d9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="693d9-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="693d9-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="693d9-106">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="693d9-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="693d9-107">*Esta sección es aplicable a Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="693d9-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="693d9-108">Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="693d9-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="693d9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="693d9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="693d9-110">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="693d9-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="693d9-111">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="693d9-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="693d9-112">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="693d9-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="693d9-113">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="693d9-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="693d9-114">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="693d9-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="693d9-115">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="693d9-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="693d9-116">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="693d9-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="693d9-117">Agregue lo siguiente a la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="693d9-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="693d9-118">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="693d9-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="693d9-119">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="693d9-119">CSS class</span></span>                       | <span data-ttu-id="693d9-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="693d9-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="693d9-121">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="693d9-121">A lost connection.</span></span> <span data-ttu-id="693d9-122">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="693d9-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="693d9-123">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="693d9-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="693d9-124">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="693d9-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="693d9-125">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="693d9-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="693d9-126">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="693d9-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="693d9-127">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="693d9-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="693d9-128">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="693d9-128">Reconnection rejected.</span></span> <span data-ttu-id="693d9-129">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="693d9-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="693d9-130">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="693d9-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="693d9-131">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="693d9-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="693d9-132">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="693d9-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="693d9-133">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="693d9-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="693d9-134">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="693d9-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="693d9-135">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="693d9-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="693d9-136">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="693d9-136">Render mode</span></span>

<span data-ttu-id="693d9-137">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="693d9-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="693d9-138">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="693d9-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="693d9-139">Esto se configura en la página `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="693d9-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="693d9-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="693d9-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="693d9-141">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="693d9-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="693d9-142">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="693d9-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="693d9-143">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="693d9-143">Render mode</span></span> | <span data-ttu-id="693d9-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="693d9-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="693d9-145">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="693d9-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="693d9-146">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="693d9-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="693d9-147">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="693d9-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="693d9-148">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="693d9-148">Output from the component isn't included.</span></span> <span data-ttu-id="693d9-149">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="693d9-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="693d9-150">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="693d9-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="693d9-151">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="693d9-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="693d9-152">Configuración del cliente de SignalR para aplicaciones Blazor Server</span><span class="sxs-lookup"><span data-stu-id="693d9-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="693d9-153">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="693d9-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="693d9-154">Configure el cliente SignalR que usan las aplicaciones Blazor Server en el archivo `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="693d9-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="693d9-155">Coloque un script que llame a `Blazor.start` después del script `_framework/blazor.server.js` y dentro de la etiqueta `</body>`.</span><span class="sxs-lookup"><span data-stu-id="693d9-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="693d9-156">Registro</span><span class="sxs-lookup"><span data-stu-id="693d9-156">Logging</span></span>

<span data-ttu-id="693d9-157">Para configurar el registro de cliente de SignalR:</span><span class="sxs-lookup"><span data-stu-id="693d9-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="693d9-158">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="693d9-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="693d9-159">Pase un objeto de configuración (`configureSignalR`) que llame a `configureLogging` con el nivel de registro en el generador de cliente.</span><span class="sxs-lookup"><span data-stu-id="693d9-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

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

<span data-ttu-id="693d9-160">En el ejemplo anterior, `information` es equivalente a un nivel de registro de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="693d9-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="693d9-161">Modificación del controlador de reconexión</span><span class="sxs-lookup"><span data-stu-id="693d9-161">Modify the reconnection handler</span></span>

<span data-ttu-id="693d9-162">Los eventos de conexión del circuito del controlador de reconexión pueden modificarse para obtener comportamientos personalizados, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="693d9-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="693d9-163">Para notificar al usuario si la conexión se ha quitado.</span><span class="sxs-lookup"><span data-stu-id="693d9-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="693d9-164">Para realizar el registro (desde el cliente) cuando un circuito está conectado.</span><span class="sxs-lookup"><span data-stu-id="693d9-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="693d9-165">Para modificar los eventos de conexión:</span><span class="sxs-lookup"><span data-stu-id="693d9-165">To modify the connection events:</span></span>

* <span data-ttu-id="693d9-166">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="693d9-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="693d9-167">Registre las devoluciones de llamada de los cambios de conexión para las conexiones quitadas (`onConnectionDown`) y las conexiones establecidas/restablecidas (`onConnectionUp`).</span><span class="sxs-lookup"><span data-stu-id="693d9-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="693d9-168">Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="693d9-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="693d9-169">Ajustar el número y el intervalo de reintentos de reconexión</span><span class="sxs-lookup"><span data-stu-id="693d9-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="693d9-170">Para ajustar el número y el intervalo de reintentos de reconexión, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="693d9-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="693d9-171">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="693d9-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="693d9-172">Establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`).</span><span class="sxs-lookup"><span data-stu-id="693d9-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="693d9-173">Ocultar o reemplazar la pantalla de reconexión</span><span class="sxs-lookup"><span data-stu-id="693d9-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="693d9-174">Para ocultar la pantalla de reconexión:</span><span class="sxs-lookup"><span data-stu-id="693d9-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="693d9-175">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="693d9-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="693d9-176">Establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`).</span><span class="sxs-lookup"><span data-stu-id="693d9-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

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

<span data-ttu-id="693d9-177">Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:</span><span class="sxs-lookup"><span data-stu-id="693d9-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="693d9-178">El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.</span><span class="sxs-lookup"><span data-stu-id="693d9-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="693d9-179">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="693d9-179">Additional resources</span></span>

* <xref:fundamentals/logging/index>
