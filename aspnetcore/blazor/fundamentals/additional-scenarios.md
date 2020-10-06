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
ms.openlocfilehash: 236d95e54b772ea522911421084ec0d9022c45ff
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424144"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="a68b5-103">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a68b5-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="a68b5-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a68b5-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a68b5-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="a68b5-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="a68b5-106">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="a68b5-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="a68b5-107">*Esta sección es aplicable a Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="a68b5-108">Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="a68b5-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="a68b5-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="a68b5-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="a68b5-110">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="a68b5-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="a68b5-111">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="a68b5-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a68b5-112">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="a68b5-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a68b5-113">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a68b5-114">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="a68b5-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a68b5-115">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="a68b5-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a68b5-116">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="a68b5-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a68b5-117">Agregue lo siguiente a la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="a68b5-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="a68b5-118">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a68b5-119">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="a68b5-119">CSS class</span></span>                       | <span data-ttu-id="a68b5-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="a68b5-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="a68b5-121">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="a68b5-121">A lost connection.</span></span> <span data-ttu-id="a68b5-122">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="a68b5-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="a68b5-123">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="a68b5-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a68b5-124">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="a68b5-125">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="a68b5-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a68b5-126">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="a68b5-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a68b5-127">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a68b5-128">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="a68b5-128">Reconnection rejected.</span></span> <span data-ttu-id="a68b5-129">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a68b5-130">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a68b5-131">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="a68b5-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="a68b5-132">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a68b5-133">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="a68b5-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a68b5-134">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="a68b5-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a68b5-135">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a68b5-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="a68b5-136">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="a68b5-136">Render mode</span></span>

<span data-ttu-id="a68b5-137">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a68b5-138">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="a68b5-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a68b5-139">Esto se configura en la página `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="a68b5-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="a68b5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="a68b5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="a68b5-141">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="a68b5-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="a68b5-142">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="a68b5-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="a68b5-143">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="a68b5-143">Render mode</span></span> | <span data-ttu-id="a68b5-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="a68b5-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a68b5-145">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a68b5-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a68b5-146">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a68b5-147">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a68b5-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a68b5-148">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="a68b5-148">Output from the component isn't included.</span></span> <span data-ttu-id="a68b5-149">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a68b5-150">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a68b5-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="a68b5-151">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="a68b5-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="a68b5-152">Inicialización del circuito de Blazor</span><span class="sxs-lookup"><span data-stu-id="a68b5-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="a68b5-153">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a68b5-154">Configure el inicio manual de un [circuito de SignalR](xref:blazor/hosting-models#circuits) de la aplicación Blazor Server en el archivo `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a68b5-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="a68b5-155">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a68b5-156">Coloque un script que llame a `Blazor.start` después de la etiqueta del script `blazor.server.js` y dentro de la etiqueta de cierre `</body>`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="a68b5-157">Cuando `autostart` está deshabilitado, cualquier aspecto de la aplicación que no depende del circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="a68b5-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="a68b5-158">Por ejemplo, el enrutamiento del lado cliente está operativo.</span><span class="sxs-lookup"><span data-stu-id="a68b5-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="a68b5-159">Sin embargo, cualquier aspecto que dependa del circuito no funcionará hasta que se llame a `Blazor.start`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="a68b5-160">El comportamiento de la aplicación es imprevisible sin ningún circuito establecido.</span><span class="sxs-lookup"><span data-stu-id="a68b5-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="a68b5-161">Por ejemplo, los métodos de componente no se ejecutan mientras el circuito está desconectado.</span><span class="sxs-lookup"><span data-stu-id="a68b5-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="a68b5-162">Inicialización de Blazor cuando el documento está listo</span><span class="sxs-lookup"><span data-stu-id="a68b5-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="a68b5-163">Para inicializar la aplicación Blazor cuando el documento está listo:</span><span class="sxs-lookup"><span data-stu-id="a68b5-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="a68b5-164">Cadena al elemento `Promise` resultante de un inicio manual</span><span class="sxs-lookup"><span data-stu-id="a68b5-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="a68b5-165">Para realizar tareas adicionales, como la inicialización de interoperabilidad de JS, use `then` como cadena al elemento `Promise` resultante de un inicio manual de la aplicación Blazor:</span><span class="sxs-lookup"><span data-stu-id="a68b5-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="a68b5-166">Configuración del cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="a68b5-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="a68b5-167">Registro</span><span class="sxs-lookup"><span data-stu-id="a68b5-167">Logging</span></span>

<span data-ttu-id="a68b5-168">Para configurar el registro del cliente SignalR, pase un objeto de configuración (`configureSignalR`) que llame a `configureLogging` con el nivel de registro en el generador de cliente:</span><span class="sxs-lookup"><span data-stu-id="a68b5-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="a68b5-169">En el ejemplo anterior, `information` es equivalente a un nivel de registro de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="a68b5-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="a68b5-170">Modificación del controlador de reconexión</span><span class="sxs-lookup"><span data-stu-id="a68b5-170">Modify the reconnection handler</span></span>

<span data-ttu-id="a68b5-171">Los eventos de conexión del circuito del controlador de reconexión pueden modificarse para obtener comportamientos personalizados, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a68b5-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="a68b5-172">Para notificar al usuario si la conexión se ha quitado.</span><span class="sxs-lookup"><span data-stu-id="a68b5-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="a68b5-173">Para realizar el registro (desde el cliente) cuando un circuito está conectado.</span><span class="sxs-lookup"><span data-stu-id="a68b5-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="a68b5-174">Para modificar los eventos de conexión, registre las devoluciones de llamada para los siguientes cambios de conexión:</span><span class="sxs-lookup"><span data-stu-id="a68b5-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="a68b5-175">Las conexiones que se quitan usan `onConnectionDown`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="a68b5-176">Las conexiones establecidas o restablecidas usan `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="a68b5-177">Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`:</span><span class="sxs-lookup"><span data-stu-id="a68b5-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="a68b5-178">Ajustar el número y el intervalo de reintentos de reconexión</span><span class="sxs-lookup"><span data-stu-id="a68b5-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="a68b5-179">Para ajustar el intervalo y el número de reintentos de reconexión, establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`):</span><span class="sxs-lookup"><span data-stu-id="a68b5-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="a68b5-180">Ocultar o reemplazar la pantalla de reconexión</span><span class="sxs-lookup"><span data-stu-id="a68b5-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="a68b5-181">Para ocultar la presentación de reconexión, establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`):</span><span class="sxs-lookup"><span data-stu-id="a68b5-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="a68b5-182">Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:</span><span class="sxs-lookup"><span data-stu-id="a68b5-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="a68b5-183">El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.</span><span class="sxs-lookup"><span data-stu-id="a68b5-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a68b5-184">Personalice el retraso antes de que aparezca la pantalla de reconexión. Para ello, establezca la propiedad `transition-delay` en el CSS (`wwwroot/css/site.css`) de la aplicación como elemento modal.</span><span class="sxs-lookup"><span data-stu-id="a68b5-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="a68b5-185">En el ejemplo siguiente se establece el retraso de transición entre 500 ms (valor predeterminado) y 1000 ms (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="a68b5-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="a68b5-186">Desconexión del circuito Blazor del cliente</span><span class="sxs-lookup"><span data-stu-id="a68b5-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="a68b5-187">De forma predeterminada, un circuito Blazor se desconecta cuando se desencadena el [evento de página `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event).</span><span class="sxs-lookup"><span data-stu-id="a68b5-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="a68b5-188">A fin de desconectar el circuito para otros escenarios en el cliente, invoque `Blazor.disconnect` en el controlador de eventos adecuado.</span><span class="sxs-lookup"><span data-stu-id="a68b5-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="a68b5-189">En el ejemplo siguiente, el circuito se desconecta cuando la página está oculta ([evento `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="a68b5-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="a68b5-190">Influencia en los elementos de etiqueta `<head>`</span><span class="sxs-lookup"><span data-stu-id="a68b5-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="a68b5-191">*Esta sección se aplica a la próxima versión de ASP.NET Core 5.0 de Blazor WebAssembly y Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="a68b5-192">Cuando se representan, los componentes `Title`, `Link` y `Meta` agregan o actualizan los datos en los elementos de etiqueta `<head>` HTML:</span><span class="sxs-lookup"><span data-stu-id="a68b5-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="a68b5-193">En el ejemplo anterior, los marcadores de posición de `{TITLE}`, `{URL}` y `{DESCRIPTION}` son valores de cadena, variables de Razor o expresiones de Razor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="a68b5-194">Se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="a68b5-194">The following characteristics apply:</span></span>

* <span data-ttu-id="a68b5-195">Se admite la representación previa del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="a68b5-196">El parámetro `Value` es el único parámetro válido para el componente `Title`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="a68b5-197">Los atributos HTML proporcionados a los componentes `Meta` y `Link` se capturan en [atributos adicionales](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) y se pasan a la etiqueta HTML representada.</span><span class="sxs-lookup"><span data-stu-id="a68b5-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="a68b5-198">Si se trata de varios componentes `Title`, el título de la página refleja el elemento `Value` del último componente `Title` representado.</span><span class="sxs-lookup"><span data-stu-id="a68b5-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="a68b5-199">Si se incluyen varios componentes `Meta` o `Link` con atributos idénticos, hay exactamente una etiqueta HTML representada por componente `Meta` o `Link`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="a68b5-200">Dos componentes `Meta` o `Link` no pueden hacer referencia a la misma etiqueta HTML representada.</span><span class="sxs-lookup"><span data-stu-id="a68b5-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="a68b5-201">Los cambios en los parámetros de los componentes `Meta` o `Link` existentes se reflejan en sus etiquetas HTML representadas.</span><span class="sxs-lookup"><span data-stu-id="a68b5-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="a68b5-202">Cuando los componentes `Link` o `Meta` ya no se representan y, por lo tanto, el marco de trabajo los elimina, se quitan sus etiquetas HTML representadas.</span><span class="sxs-lookup"><span data-stu-id="a68b5-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="a68b5-203">Cuando se usa uno de los componentes de .NET Framework en un componente secundario, la etiqueta HTML representada influye en cualquier otro componente secundario del componente primario, siempre que se represente el componente secundario que contiene el componente del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a68b5-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="a68b5-204">La distinción entre el uso de uno de estos componentes del marco de trabajo en un componente secundario y la colocación de una etiqueta HTML en `wwwroot/index.html` o `Pages/_Host.cshtml` es que la etiqueta HTML representada de un componente del marco de trabajo:</span><span class="sxs-lookup"><span data-stu-id="a68b5-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="a68b5-205">Se puede modificar según el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a68b5-205">Can be modified by application state.</span></span> <span data-ttu-id="a68b5-206">No se puede modificar una etiqueta HTML codificada de forma rígida según el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a68b5-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="a68b5-207">Se quita del elemento HTML `<head>` cuando ya no se representa el componente primario.</span><span class="sxs-lookup"><span data-stu-id="a68b5-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="a68b5-208">Archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="a68b5-208">Static files</span></span>

<span data-ttu-id="a68b5-209">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="a68b5-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a68b5-210">Para crear asignaciones de archivos adicionales con un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurar otros valores <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **uno** de los enfoques siguientes.</span><span class="sxs-lookup"><span data-stu-id="a68b5-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="a68b5-211">En los ejemplos siguientes, el marcador de posición `{EXTENSION}` es la extensión de archivo y `{CONTENT TYPE}` es el tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="a68b5-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="a68b5-212">Configure las opciones a través de la [inserción de dependencias (DI)](xref:blazor/fundamentals/dependency-injection) en `Startup.ConfigureServices` (`Startup.cs`) mediante <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span><span class="sxs-lookup"><span data-stu-id="a68b5-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="a68b5-213">Como este enfoque configura el mismo proveedor de archivos que se ha usado para proporcionar `blazor.server.js`, asegúrese de que la configuración personalizada no interfiera con la acción de proporcionar `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="a68b5-214">Por ejemplo, no quite la asignación para los archivos JavaScript mediante la configuración del proveedor con `provider.Mappings.Remove(".js")`.</span><span class="sxs-lookup"><span data-stu-id="a68b5-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="a68b5-215">Use dos llamadas a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="a68b5-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="a68b5-216">Configure el proveedor de archivos personalizado en la primera llamada con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="a68b5-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="a68b5-217">El segundo middleware proporciona `blazor.server.js`, que usa la configuración predeterminada de los archivos estáticos proporcionada por el marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="a68b5-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="a68b5-218">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a68b5-218">Additional resources</span></span>

* <xref:fundamentals/logging/index>
