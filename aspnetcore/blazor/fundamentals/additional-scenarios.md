---
title: 'Configuración del modelo de hospedaje de :::no-loc(Blazor)::: en ASP.NET Core'
author: guardrex
description: 'Obtenga información sobre más escenarios de configuración del modelo de hospedaje de :::no-loc(Blazor)::: en ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 075bcc68fd2dff0ebf2cfceacec24fde8c818603
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326535"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="de1c9-103">Configuración del modelo de hospedaje de :::no-loc(Blazor)::: en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de1c9-103">ASP.NET Core :::no-loc(Blazor)::: hosting model configuration</span></span>

<span data-ttu-id="de1c9-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de1c9-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="de1c9-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="de1c9-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="de1c9-106">Negociación entre orígenes de :::no-loc(SignalR)::: para la autenticación</span><span class="sxs-lookup"><span data-stu-id="de1c9-106">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>

<span data-ttu-id="de1c9-107">*Esta sección es aplicable a :::no-loc(Blazor WebAssembly):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-107">*This section applies to :::no-loc(Blazor WebAssembly):::.*</span></span>

<span data-ttu-id="de1c9-108">Para configurar el cliente subyacente de :::no-loc(SignalR)::: para que envíe credenciales, como :::no-loc(cookie):::s o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="de1c9-108">To configure :::no-loc(SignalR):::'s underlying client to send credentials, such as :::no-loc(cookie):::s or HTTP authentication headers:</span></span>

* <span data-ttu-id="de1c9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="de1c9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="de1c9-110">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="de1c9-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="de1c9-111">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="de1c9-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="de1c9-112">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="de1c9-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="de1c9-113">*Esta sección es aplicable a :::no-loc(Blazor Server):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-113">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="de1c9-114">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="de1c9-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="de1c9-115">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="de1c9-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="de1c9-116">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="de1c9-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` :::no-loc(Razor)::: page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="de1c9-117">Agregue lo siguiente a la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="de1c9-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="de1c9-118">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="de1c9-119">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="de1c9-119">CSS class</span></span>                       | <span data-ttu-id="de1c9-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="de1c9-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="de1c9-121">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="de1c9-121">A lost connection.</span></span> <span data-ttu-id="de1c9-122">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="de1c9-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="de1c9-123">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="de1c9-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="de1c9-124">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="de1c9-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="de1c9-125">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="de1c9-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="de1c9-126">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="de1c9-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="de1c9-127">Para intentar la reconexión, llame a `window.:::no-loc(Blazor):::.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-127">To attempt reconnection, call `window.:::no-loc(Blazor):::.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="de1c9-128">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="de1c9-128">Reconnection rejected.</span></span> <span data-ttu-id="de1c9-129">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="de1c9-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="de1c9-130">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="de1c9-131">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="de1c9-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="de1c9-132">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="de1c9-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="de1c9-133">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="de1c9-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="de1c9-134">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="de1c9-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="de1c9-135">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="de1c9-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="de1c9-136">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="de1c9-136">Render mode</span></span>

<span data-ttu-id="de1c9-137">*Esta sección es aplicable a :::no-loc(Blazor Server):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-137">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="de1c9-138">Las aplicaciones :::no-loc(Blazor Server)::: se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="de1c9-138">:::no-loc(Blazor Server)::: apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="de1c9-139">Esto se configura en la página `_Host.cshtml` :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="de1c9-139">This is set up in the `_Host.cshtml` :::no-loc(Razor)::: page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="de1c9-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="de1c9-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="de1c9-141">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="de1c9-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="de1c9-142">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación :::no-loc(Blazor)::: desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="de1c9-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

| <span data-ttu-id="de1c9-143">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="de1c9-143">Render mode</span></span> | <span data-ttu-id="de1c9-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="de1c9-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="de1c9-145">Representa el componente en código HTML estático e incluye un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-145">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="de1c9-146">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-146">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="de1c9-147">Representa un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-147">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="de1c9-148">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="de1c9-148">Output from the component isn't included.</span></span> <span data-ttu-id="de1c9-149">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-149">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="de1c9-150">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="de1c9-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="de1c9-151">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="de1c9-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="de1c9-152">Inicialización del circuito de :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="de1c9-152">Initialize the :::no-loc(Blazor)::: circuit</span></span>

<span data-ttu-id="de1c9-153">*Esta sección es aplicable a :::no-loc(Blazor Server):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-153">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="de1c9-154">Configure el inicio manual de un [circuito de :::no-loc(SignalR):::](xref:blazor/hosting-models#circuits) de la aplicación :::no-loc(Blazor Server)::: en el archivo `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="de1c9-154">Configure the manual start of a :::no-loc(Blazor Server)::: app's [:::no-loc(SignalR)::: circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="de1c9-155">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="de1c9-156">Coloque un script que llame a `:::no-loc(Blazor):::.start` después de la etiqueta del script `blazor.server.js` y dentro de la etiqueta de cierre `</body>`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-156">Place a script that calls `:::no-loc(Blazor):::.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="de1c9-157">Cuando `autostart` está deshabilitado, cualquier aspecto de la aplicación que no depende del circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="de1c9-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="de1c9-158">Por ejemplo, el enrutamiento del lado cliente está operativo.</span><span class="sxs-lookup"><span data-stu-id="de1c9-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="de1c9-159">Sin embargo, cualquier aspecto que dependa del circuito no funcionará hasta que se llame a `:::no-loc(Blazor):::.start`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-159">However, any aspect that depends on the circuit isn't operational until `:::no-loc(Blazor):::.start` is called.</span></span> <span data-ttu-id="de1c9-160">El comportamiento de la aplicación es imprevisible sin ningún circuito establecido.</span><span class="sxs-lookup"><span data-stu-id="de1c9-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="de1c9-161">Por ejemplo, los métodos de componente no se ejecutan mientras el circuito está desconectado.</span><span class="sxs-lookup"><span data-stu-id="de1c9-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="de1c9-162">Inicialización de :::no-loc(Blazor)::: cuando el documento está listo</span><span class="sxs-lookup"><span data-stu-id="de1c9-162">Initialize :::no-loc(Blazor)::: when the document is ready</span></span>

<span data-ttu-id="de1c9-163">Para inicializar la aplicación :::no-loc(Blazor)::: cuando el documento está listo:</span><span class="sxs-lookup"><span data-stu-id="de1c9-163">To initialize the :::no-loc(Blazor)::: app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        :::no-loc(Blazor):::.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="de1c9-164">Cadena al elemento `Promise` resultante de un inicio manual</span><span class="sxs-lookup"><span data-stu-id="de1c9-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="de1c9-165">Para realizar tareas adicionales, como la inicialización de interoperabilidad de JS, use `then` como cadena al elemento `Promise` resultante de un inicio manual de la aplicación :::no-loc(Blazor)::::</span><span class="sxs-lookup"><span data-stu-id="de1c9-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual :::no-loc(Blazor)::: app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="de1c9-166">Configuración del cliente de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="de1c9-166">Configure the :::no-loc(SignalR)::: client</span></span>

#### <a name="logging"></a><span data-ttu-id="de1c9-167">Registro</span><span class="sxs-lookup"><span data-stu-id="de1c9-167">Logging</span></span>

<span data-ttu-id="de1c9-168">Para configurar el registro del cliente :::no-loc(SignalR):::, pase un objeto de configuración (`configure:::no-loc(SignalR):::`) que llame a `configureLogging` con el nivel de registro en el generador de cliente:</span><span class="sxs-lookup"><span data-stu-id="de1c9-168">To configure :::no-loc(SignalR)::: client logging, pass in a configuration object (`configure:::no-loc(SignalR):::`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        configure:::no-loc(SignalR):::: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="de1c9-169">En el ejemplo anterior, `information` es equivalente a un nivel de registro de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="de1c9-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="de1c9-170">Modificación del controlador de reconexión</span><span class="sxs-lookup"><span data-stu-id="de1c9-170">Modify the reconnection handler</span></span>

<span data-ttu-id="de1c9-171">Los eventos de conexión del circuito del controlador de reconexión pueden modificarse para obtener comportamientos personalizados, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="de1c9-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="de1c9-172">Para notificar al usuario si la conexión se ha quitado.</span><span class="sxs-lookup"><span data-stu-id="de1c9-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="de1c9-173">Para realizar el registro (desde el cliente) cuando un circuito está conectado.</span><span class="sxs-lookup"><span data-stu-id="de1c9-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="de1c9-174">Para modificar los eventos de conexión, registre las devoluciones de llamada para los siguientes cambios de conexión:</span><span class="sxs-lookup"><span data-stu-id="de1c9-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="de1c9-175">Las conexiones que se quitan usan `onConnectionDown`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="de1c9-176">Las conexiones establecidas o restablecidas usan `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="de1c9-177">Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`:</span><span class="sxs-lookup"><span data-stu-id="de1c9-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="de1c9-178">Ajustar el número y el intervalo de reintentos de reconexión</span><span class="sxs-lookup"><span data-stu-id="de1c9-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="de1c9-179">Para ajustar el intervalo y el número de reintentos de reconexión, establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`):</span><span class="sxs-lookup"><span data-stu-id="de1c9-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="de1c9-180">Ocultar o reemplazar la pantalla de reconexión</span><span class="sxs-lookup"><span data-stu-id="de1c9-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="de1c9-181">Para ocultar la presentación de reconexión, establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`):</span><span class="sxs-lookup"><span data-stu-id="de1c9-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        :::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      :::no-loc(Blazor):::.start();
    </script>
</body>
```

<span data-ttu-id="de1c9-182">Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:</span><span class="sxs-lookup"><span data-stu-id="de1c9-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
:::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="de1c9-183">El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.</span><span class="sxs-lookup"><span data-stu-id="de1c9-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="de1c9-184">Personalice el retraso antes de que aparezca la pantalla de reconexión. Para ello, establezca la propiedad `transition-delay` en el CSS (`wwwroot/css/site.css`) de la aplicación como elemento modal.</span><span class="sxs-lookup"><span data-stu-id="de1c9-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="de1c9-185">En el ejemplo siguiente se establece el retraso de transición entre 500 ms (valor predeterminado) y 1000 ms (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="de1c9-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="de1c9-186">Desconexión del circuito :::no-loc(Blazor)::: del cliente</span><span class="sxs-lookup"><span data-stu-id="de1c9-186">Disconnect the :::no-loc(Blazor)::: circuit from the client</span></span>

<span data-ttu-id="de1c9-187">De forma predeterminada, un circuito :::no-loc(Blazor)::: se desconecta cuando se desencadena el [evento de página `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event).</span><span class="sxs-lookup"><span data-stu-id="de1c9-187">By default, a :::no-loc(Blazor)::: circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="de1c9-188">A fin de desconectar el circuito para otros escenarios en el cliente, invoque `:::no-loc(Blazor):::.disconnect` en el controlador de eventos adecuado.</span><span class="sxs-lookup"><span data-stu-id="de1c9-188">To disconnect the circuit for other scenarios on the client, invoke `:::no-loc(Blazor):::.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="de1c9-189">En el ejemplo siguiente, el circuito se desconecta cuando la página está oculta ([evento `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="de1c9-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  :::no-loc(Blazor):::.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="de1c9-190">Influencia en los elementos de etiqueta `<head>`</span><span class="sxs-lookup"><span data-stu-id="de1c9-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="de1c9-191">*Esta sección se aplica a la próxima versión de ASP.NET Core 5.0 de :::no-loc(Blazor WebAssembly)::: y :::no-loc(Blazor Server):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-191">*This section applies to the upcoming ASP.NET Core 5.0 release of :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="de1c9-192">Cuando se representan, los componentes `Title`, `Link` y `Meta` agregan o actualizan los datos en los elementos de etiqueta `<head>` HTML:</span><span class="sxs-lookup"><span data-stu-id="de1c9-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="de1c9-193">En el ejemplo anterior, los marcadores de posición de `{TITLE}`, `{URL}` y `{DESCRIPTION}` son valores de cadena, variables de :::no-loc(Razor)::: o expresiones de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, :::no-loc(Razor)::: variables, or :::no-loc(Razor)::: expressions.</span></span>

<span data-ttu-id="de1c9-194">Se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="de1c9-194">The following characteristics apply:</span></span>

* <span data-ttu-id="de1c9-195">Se admite la representación previa del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="de1c9-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="de1c9-196">El parámetro `Value` es el único parámetro válido para el componente `Title`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="de1c9-197">Los atributos HTML proporcionados a los componentes `Meta` y `Link` se capturan en [atributos adicionales](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) y se pasan a la etiqueta HTML representada.</span><span class="sxs-lookup"><span data-stu-id="de1c9-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="de1c9-198">Si se trata de varios componentes `Title`, el título de la página refleja el elemento `Value` del último componente `Title` representado.</span><span class="sxs-lookup"><span data-stu-id="de1c9-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="de1c9-199">Si se incluyen varios componentes `Meta` o `Link` con atributos idénticos, hay exactamente una etiqueta HTML representada por componente `Meta` o `Link`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="de1c9-200">Dos componentes `Meta` o `Link` no pueden hacer referencia a la misma etiqueta HTML representada.</span><span class="sxs-lookup"><span data-stu-id="de1c9-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="de1c9-201">Los cambios en los parámetros de los componentes `Meta` o `Link` existentes se reflejan en sus etiquetas HTML representadas.</span><span class="sxs-lookup"><span data-stu-id="de1c9-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="de1c9-202">Cuando los componentes `Link` o `Meta` ya no se representan y, por lo tanto, el marco de trabajo los elimina, se quitan sus etiquetas HTML representadas.</span><span class="sxs-lookup"><span data-stu-id="de1c9-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="de1c9-203">Cuando se usa uno de los componentes de .NET Framework en un componente secundario, la etiqueta HTML representada influye en cualquier otro componente secundario del componente primario, siempre que se represente el componente secundario que contiene el componente del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="de1c9-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="de1c9-204">La distinción entre el uso de uno de estos componentes del marco de trabajo en un componente secundario y la colocación de una etiqueta HTML en `wwwroot/index.html` o `Pages/_Host.cshtml` es que la etiqueta HTML representada de un componente del marco de trabajo:</span><span class="sxs-lookup"><span data-stu-id="de1c9-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="de1c9-205">Se puede modificar según el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="de1c9-205">Can be modified by application state.</span></span> <span data-ttu-id="de1c9-206">No se puede modificar una etiqueta HTML codificada de forma rígida según el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="de1c9-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="de1c9-207">Se quita del elemento HTML `<head>` cuando ya no se representa el componente primario.</span><span class="sxs-lookup"><span data-stu-id="de1c9-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="de1c9-208">Archivos estáticos</span><span class="sxs-lookup"><span data-stu-id="de1c9-208">Static files</span></span>

<span data-ttu-id="de1c9-209">*Esta sección es aplicable a :::no-loc(Blazor Server):::.*</span><span class="sxs-lookup"><span data-stu-id="de1c9-209">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="de1c9-210">Para crear asignaciones de archivos adicionales con un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurar otros valores <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **uno** de los enfoques siguientes.</span><span class="sxs-lookup"><span data-stu-id="de1c9-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="de1c9-211">En los ejemplos siguientes, el marcador de posición `{EXTENSION}` es la extensión de archivo y `{CONTENT TYPE}` es el tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="de1c9-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="de1c9-212">Configure las opciones a través de la [inserción de dependencias (DI)](xref:blazor/fundamentals/dependency-injection) en `Startup.ConfigureServices` (`Startup.cs`) mediante <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span><span class="sxs-lookup"><span data-stu-id="de1c9-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="de1c9-213">Como este enfoque configura el mismo proveedor de archivos que se ha usado para proporcionar `blazor.server.js`, asegúrese de que la configuración personalizada no interfiera con la acción de proporcionar `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="de1c9-214">Por ejemplo, no quite la asignación para los archivos JavaScript mediante la configuración del proveedor con `provider.Mappings.Remove(".js")`.</span><span class="sxs-lookup"><span data-stu-id="de1c9-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="de1c9-215">Use dos llamadas a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="de1c9-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="de1c9-216">Configure el proveedor de archivos personalizado en la primera llamada con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="de1c9-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="de1c9-217">El segundo middleware proporciona `blazor.server.js`, que usa la configuración predeterminada de los archivos estáticos proporcionada por el marco :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="de1c9-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the :::no-loc(Blazor)::: framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="de1c9-218">Puede evitar interferir con el servicio de `_framework/blazor.server.js` mediante <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> para ejecutar un middleware de archivos estáticos personalizado:</span><span class="sxs-lookup"><span data-stu-id="de1c9-218">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="de1c9-219">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="de1c9-219">Additional resources</span></span>

* <xref:fundamentals/logging/index>
