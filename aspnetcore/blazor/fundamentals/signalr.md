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
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="6c78b-103">Guía de SignalR de Blazor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c78b-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="6c78b-104">Para obtener instrucciones generales sobre la configuración de SignalR para ASP.NET Core, consulte los temas del área <xref:signalr/introduction> de la documentación.</span><span class="sxs-lookup"><span data-stu-id="6c78b-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="6c78b-105">Para configurar SignalR [agregado a una solución de Blazor WebAssembly hospedada](xref:tutorials/signalr-blazor), vea <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="6c78b-106">Opciones de controlador de circuito</span><span class="sxs-lookup"><span data-stu-id="6c78b-106">Circuit handler options</span></span>

<span data-ttu-id="6c78b-107">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6c78b-108">Configure el circuito Blazor Server con los valores de <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="6c78b-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="6c78b-109">Opción</span><span class="sxs-lookup"><span data-stu-id="6c78b-109">Option</span></span> | <span data-ttu-id="6c78b-110">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="6c78b-110">Default</span></span> | <span data-ttu-id="6c78b-111">Descripción</span><span class="sxs-lookup"><span data-stu-id="6c78b-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="6c78b-112">Envío de mensajes de excepción detallados a JavaScript cuando se produce una excepción no controlada en el circuito o cuando una invocación de método de .NET a través de la interoperabilidad de JS produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="6c78b-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="6c78b-113">100</span><span class="sxs-lookup"><span data-stu-id="6c78b-113">100</span></span> | <span data-ttu-id="6c78b-114">Número máximo de circuitos desconectados que contiene a la vez un servidor determinado en la memoria.</span><span class="sxs-lookup"><span data-stu-id="6c78b-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="6c78b-115">3 minutos</span><span class="sxs-lookup"><span data-stu-id="6c78b-115">3 minutes</span></span> | <span data-ttu-id="6c78b-116">Cantidad máxima de tiempo que un circuito desconectado se conserva en la memoria antes de desactivarlo.</span><span class="sxs-lookup"><span data-stu-id="6c78b-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="6c78b-117">1 minuto</span><span class="sxs-lookup"><span data-stu-id="6c78b-117">1 minute</span></span> | <span data-ttu-id="6c78b-118">Cantidad máxima de tiempo que el servidor aguarda antes de agotar el tiempo de espera de invocación de una función de JavaScript asincrónica.</span><span class="sxs-lookup"><span data-stu-id="6c78b-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="6c78b-119">10</span><span class="sxs-lookup"><span data-stu-id="6c78b-119">10</span></span> | <span data-ttu-id="6c78b-120">Número máximo de lotes de representación no confirmados por circuito que el servidor mantiene en la memoria en un momento dado para admitir una reconexión sólida.</span><span class="sxs-lookup"><span data-stu-id="6c78b-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="6c78b-121">Después de alcanzar el límite, el servidor deja de generar nuevos lotes de representación hasta que el cliente confirma uno o varios lotes.</span><span class="sxs-lookup"><span data-stu-id="6c78b-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="6c78b-122">Configure las opciones de `Startup.ConfigureServices` con un delegado de opciones en <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="6c78b-123">En el ejemplo siguiente se asignan los valores de opción predeterminados que se muestran en la tabla anterior:</span><span class="sxs-lookup"><span data-stu-id="6c78b-123">The following example assigns the default option values shown in the preceding table:</span></span>

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

<span data-ttu-id="6c78b-124">Para configurar <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> con <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="6c78b-125">Para obtener descripciones de las opciones, vea <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="6c78b-126">En el ejemplo siguiente se asignan los valores de opción predeterminados:</span><span class="sxs-lookup"><span data-stu-id="6c78b-126">The following example assigns the default option values:</span></span>

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="6c78b-127">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="6c78b-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="6c78b-128">*Esta sección es aplicable a Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="6c78b-129">Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="6c78b-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="6c78b-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="6c78b-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="6c78b-131">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="6c78b-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="6c78b-132">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="6c78b-133">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="6c78b-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="6c78b-134">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6c78b-135">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="6c78b-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="6c78b-136">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="6c78b-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="6c78b-137">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de `_Host.cshtml` Razor:</span><span class="sxs-lookup"><span data-stu-id="6c78b-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="6c78b-138">Agregue lo siguiente a la hoja de estilos de la aplicación (`wwwroot/css/app.css` o `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="6c78b-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="6c78b-139">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="6c78b-140">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="6c78b-140">CSS class</span></span>                       | <span data-ttu-id="6c78b-141">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="6c78b-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="6c78b-142">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="6c78b-142">A lost connection.</span></span> <span data-ttu-id="6c78b-143">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="6c78b-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="6c78b-144">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="6c78b-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="6c78b-145">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="6c78b-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="6c78b-146">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="6c78b-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="6c78b-147">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="6c78b-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="6c78b-148">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="6c78b-149">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="6c78b-149">Reconnection rejected.</span></span> <span data-ttu-id="6c78b-150">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6c78b-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="6c78b-151">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="6c78b-152">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="6c78b-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="6c78b-153">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="6c78b-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="6c78b-154">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="6c78b-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="6c78b-155">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="6c78b-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="6c78b-156">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6c78b-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="6c78b-157">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="6c78b-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6c78b-158">*Esta sección se aplica a Blazor WebAssembly y Blazor Server hospedados.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="6c78b-159">Las aplicaciones de Blazor se configuran de forma predeterminada para representar previamente la interfaz de usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="6c78b-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="6c78b-160">Para más información, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6c78b-161">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6c78b-162">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="6c78b-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="6c78b-163">Para más información, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="6c78b-164">Inicialización del circuito de Blazor</span><span class="sxs-lookup"><span data-stu-id="6c78b-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="6c78b-165">*Esta sección es aplicable a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="6c78b-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6c78b-166">Configure el inicio manual de un [circuito de SignalR](xref:blazor/hosting-models#circuits) de la aplicación Blazor Server en el archivo `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="6c78b-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="6c78b-167">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="6c78b-168">Coloque un script que llame a `Blazor.start` después de la etiqueta del script `blazor.server.js` y dentro de la etiqueta de cierre `</body>`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="6c78b-169">Cuando `autostart` está deshabilitado, cualquier aspecto de la aplicación que no depende del circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="6c78b-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="6c78b-170">Por ejemplo, el enrutamiento del lado cliente está operativo.</span><span class="sxs-lookup"><span data-stu-id="6c78b-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="6c78b-171">Sin embargo, cualquier aspecto que dependa del circuito no funcionará hasta que se llame a `Blazor.start`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="6c78b-172">El comportamiento de la aplicación es imprevisible sin ningún circuito establecido.</span><span class="sxs-lookup"><span data-stu-id="6c78b-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="6c78b-173">Por ejemplo, los métodos de componente no se ejecutan mientras el circuito está desconectado.</span><span class="sxs-lookup"><span data-stu-id="6c78b-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="6c78b-174">Inicialización de Blazor cuando el documento está listo</span><span class="sxs-lookup"><span data-stu-id="6c78b-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="6c78b-175">Para inicializar la aplicación Blazor cuando el documento está listo:</span><span class="sxs-lookup"><span data-stu-id="6c78b-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="6c78b-176">Cadena al elemento `Promise` resultante de un inicio manual</span><span class="sxs-lookup"><span data-stu-id="6c78b-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="6c78b-177">Para realizar tareas adicionales, como la inicialización de interoperabilidad de JS, use `then` como cadena al elemento `Promise` resultante de un inicio manual de la aplicación Blazor:</span><span class="sxs-lookup"><span data-stu-id="6c78b-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="6c78b-178">Configuración del cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="6c78b-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="6c78b-179">Registro</span><span class="sxs-lookup"><span data-stu-id="6c78b-179">Logging</span></span>

<span data-ttu-id="6c78b-180">Para configurar el registro del cliente SignalR, pase un objeto de configuración (`configureSignalR`) que llame a `configureLogging` con el nivel de registro en el generador de cliente:</span><span class="sxs-lookup"><span data-stu-id="6c78b-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="6c78b-181">En el ejemplo anterior, `information` es equivalente a un nivel de registro de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6c78b-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="6c78b-182">Modificación del controlador de reconexión</span><span class="sxs-lookup"><span data-stu-id="6c78b-182">Modify the reconnection handler</span></span>

<span data-ttu-id="6c78b-183">Los eventos de conexión del circuito del controlador de reconexión pueden modificarse para obtener comportamientos personalizados, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6c78b-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="6c78b-184">Para notificar al usuario si la conexión se ha quitado.</span><span class="sxs-lookup"><span data-stu-id="6c78b-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="6c78b-185">Para realizar el registro (desde el cliente) cuando un circuito está conectado.</span><span class="sxs-lookup"><span data-stu-id="6c78b-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="6c78b-186">Para modificar los eventos de conexión, registre las devoluciones de llamada para los siguientes cambios de conexión:</span><span class="sxs-lookup"><span data-stu-id="6c78b-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="6c78b-187">Las conexiones que se quitan usan `onConnectionDown`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="6c78b-188">Las conexiones establecidas o restablecidas usan `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="6c78b-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="6c78b-189">Se debe especificar **tanto** `onConnectionDown` como `onConnectionUp`:</span><span class="sxs-lookup"><span data-stu-id="6c78b-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="6c78b-190">Ajustar el número y el intervalo de reintentos de reconexión</span><span class="sxs-lookup"><span data-stu-id="6c78b-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="6c78b-191">Para ajustar el intervalo y el número de reintentos de reconexión, establezca el número de reintentos (`maxRetries`) y el período en milisegundos permitido para cada reintento (`retryIntervalMilliseconds`):</span><span class="sxs-lookup"><span data-stu-id="6c78b-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="6c78b-192">Ocultar o reemplazar la pantalla de reconexión</span><span class="sxs-lookup"><span data-stu-id="6c78b-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="6c78b-193">Para ocultar la presentación de reconexión, establezca el valor `_reconnectionDisplay` del controlador de reconexión en un objeto vacío (`{}` o `new Object()`):</span><span class="sxs-lookup"><span data-stu-id="6c78b-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="6c78b-194">Para reemplazar la pantalla de reconexión, establezca `_reconnectionDisplay` del ejemplo anterior en el elemento que se va a mostrar:</span><span class="sxs-lookup"><span data-stu-id="6c78b-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="6c78b-195">El marcador de posición `{ELEMENT ID}` es el identificador del elemento HTML que se va a mostrar.</span><span class="sxs-lookup"><span data-stu-id="6c78b-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6c78b-196">Personalice el retraso antes de que aparezca la pantalla de reconexión. Para ello, establezca la propiedad `transition-delay` en el CSS (`wwwroot/css/site.css`) de la aplicación como elemento modal.</span><span class="sxs-lookup"><span data-stu-id="6c78b-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="6c78b-197">En el ejemplo siguiente se establece el retraso de transición entre 500 ms (valor predeterminado) y 1000 ms (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="6c78b-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="6c78b-198">Desconexión del circuito Blazor del cliente</span><span class="sxs-lookup"><span data-stu-id="6c78b-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="6c78b-199">De forma predeterminada, un circuito Blazor se desconecta cuando se desencadena el [evento de página `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event).</span><span class="sxs-lookup"><span data-stu-id="6c78b-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="6c78b-200">A fin de desconectar el circuito para otros escenarios en el cliente, invoque `Blazor.disconnect` en el controlador de eventos adecuado.</span><span class="sxs-lookup"><span data-stu-id="6c78b-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="6c78b-201">En el ejemplo siguiente, el circuito se desconecta cuando la página está oculta ([evento `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="6c78b-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6c78b-202">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6c78b-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="6c78b-203">Eventos de reconexión y de ciclo de vida de componentes de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="6c78b-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
