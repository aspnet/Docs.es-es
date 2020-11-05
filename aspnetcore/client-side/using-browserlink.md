---
title: Vínculo con exploradores en ASP.NET Core
author: ncarandini
description: En este artículo se explica que Vínculo con exploradores es una característica de Visual Studio que vincula el entorno de desarrollo con uno o más exploradores web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054494"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="c1dcd-103">Vínculo con exploradores en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1dcd-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="c1dcd-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c1dcd-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="c1dcd-105">Vínculo con exploradores es una característica de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="c1dcd-106">Crea un canal de comunicación entre el entorno de desarrollo y uno o varios exploradores web.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="c1dcd-107">Puede usar Vínculo con exploradores para actualizar su aplicación web en varios exploradores a la vez, lo que resulta útil a la hora de hacer pruebas entre exploradores.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="c1dcd-108">Configuración de Vínculo con exploradores</span><span class="sxs-lookup"><span data-stu-id="c1dcd-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1dcd-109">Agregue el paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) a su proyecto.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="c1dcd-110">En los proyectos de :::no-loc(Razor)::: Pages o MVC de ASP.NET Core, habilite también la compilación en tiempo de ejecución de los archivos de :::no-loc(Razor)::: ( *.cshtml* ), como se explica en <xref:mvc/views/view-compilation>.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-110">For ASP.NET Core :::no-loc(Razor)::: Pages or MVC projects, also enable runtime compilation of :::no-loc(Razor)::: ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="c1dcd-111">Los cambios en la sintaxis de :::no-loc(Razor)::: solo se aplican cuando está habilitada la compilación en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-111">:::no-loc(Razor)::: syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c1dcd-112">Al convertir un proyecto de ASP.NET Core 2.0 en ASP.NET Core 2.1 y pasar al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), instale el paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para la funcionalidad de Vínculo con exploradores.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="c1dcd-113">De forma predeterminada, las plantillas de proyecto de ASP.NET Core 2.1 usan el metapaquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c1dcd-114">Las plantillas de proyecto **vacío** , **de aplicación web** y **API web** de ASP.NET Core 2.0 usan el [metapaquete Microsoft.AspNetCore.All](xref:fundamentals/metapackage), que contiene una referencia de paquete a [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="c1dcd-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="c1dcd-115">Por lo tanto, el uso del metapaquete `Microsoft.AspNetCore.All` no requiere ninguna acción adicional para que Vínculo con exploradores esté disponible para su uso.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c1dcd-116">La plantilla de proyecto de **aplicación web** de ASP.NET Core 1.x tiene una referencia de paquete al paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="c1dcd-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="c1dcd-117">Otros tipos de proyectos requieren que agregue una referencia de paquete a `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="c1dcd-118">Configuración</span><span class="sxs-lookup"><span data-stu-id="c1dcd-118">Configuration</span></span>

<span data-ttu-id="c1dcd-119">Llame a `UseBrowserLink` en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="c1dcd-120">Normalmente, la llamada a `UseBrowserLink` se coloca dentro de un bloque `if` que solo habilita Vínculo con exploradores en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="c1dcd-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="c1dcd-122">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="c1dcd-123">Uso de Vínculo con exploradores</span><span class="sxs-lookup"><span data-stu-id="c1dcd-123">How to use Browser Link</span></span>

<span data-ttu-id="c1dcd-124">Si tiene un proyecto de ASP.NET Core abierto, Visual Studio muestra el control de la barra de herramientas de Vínculo con exploradores junto al de **destino de depuración** :</span><span class="sxs-lookup"><span data-stu-id="c1dcd-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menú desplegable de Vínculo con exploradores](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="c1dcd-126">Desde el control de la barra de herramientas de Vínculo con exploradores, puede realizar las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="c1dcd-127">Actualizar la aplicación web en varios exploradores a la vez.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="c1dcd-128">Abrir el **panel de Vínculo con exploradores**.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="c1dcd-129">Habilitar o deshabilitar **Vínculo con exploradores**.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="c1dcd-130">Nota: Vínculo con exploradores está deshabilitado de forma predeterminada en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="c1dcd-131">Habilitar o deshabilitar la [sincronización automática de CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="c1dcd-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="c1dcd-132">Actualización de la aplicación web en varios exploradores a la vez</span><span class="sxs-lookup"><span data-stu-id="c1dcd-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="c1dcd-133">Para elegir un único explorador web que se inicie al abrir el proyecto, use el menú desplegable del control de la barra de herramientas de **destino de depuración** :</span><span class="sxs-lookup"><span data-stu-id="c1dcd-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menú desplegable F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="c1dcd-135">Para abrir varios exploradores a la vez, elija **Examinar con…** en el mismo menú desplegable.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="c1dcd-136">Mantenga presionada la tecla <kbd>Ctrl</kbd> para seleccionar los exploradores que quiera y, a continuación, haga clic en **Examinar** :</span><span class="sxs-lookup"><span data-stu-id="c1dcd-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![Apertura de muchos exploradores a la vez](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="c1dcd-138">En la siguiente captura de pantalla se muestran Visual Studio en la vista de índice y dos exploradores abiertos:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Ejemplo de sincronización con dos exploradores](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="c1dcd-140">Pase el mouse por encima del control de la barra de herramientas de Vínculo con exploradores para ver los exploradores que están conectados al proyecto:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Sugerencia que se muestra al pasar el mouse](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="c1dcd-142">Cambie la vista de índice; todos los exploradores conectados se actualizarán cuando haga clic en el botón de actualización de Vínculo con exploradores:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="c1dcd-144">La característica Vínculo con exploradores también funciona con los exploradores que se inician desde fuera de Visual Studio y navegan a la dirección URL de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="c1dcd-145">Panel de Vínculo con exploradores</span><span class="sxs-lookup"><span data-stu-id="c1dcd-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="c1dcd-146">Abra la ventana **Panel de Vínculo con exploradores** desde el menú desplegable de la característica para administrar la conexión con los exploradores abiertos:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="c1dcd-148">Si no hay ningún explorador conectado, puede iniciar una sesión que no sea de depuración seleccionando el vínculo **Ver en el explorador** :</span><span class="sxs-lookup"><span data-stu-id="c1dcd-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="c1dcd-150">De lo contrario, los exploradores conectados aparecen enumerados con la ruta de acceso a la página que se está mostrando en cada explorador:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="c1dcd-152">También puede hacer clic en un nombre de explorador para actualizar solo ese explorador.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="c1dcd-153">Habilitación o deshabilitación de Vínculo con exploradores</span><span class="sxs-lookup"><span data-stu-id="c1dcd-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="c1dcd-154">Si vuelve a habilitar Vínculo con exploradores después de deshabilitarlo, debe actualizar los exploradores para conectarlos de nuevo.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="c1dcd-155">Habilitación o deshabilitación de la sincronización automática de CSS</span><span class="sxs-lookup"><span data-stu-id="c1dcd-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="c1dcd-156">Si se habilita la sincronización automática de CSS, los exploradores conectados se actualizarán automáticamente al realizar cualquier cambio en los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="c1dcd-157">Funcionamiento</span><span class="sxs-lookup"><span data-stu-id="c1dcd-157">How it works</span></span>

<span data-ttu-id="c1dcd-158">Vínculo con exploradores usa [:::no-loc(SignalR):::](xref:signalr/introduction) para crear un canal de comunicación entre Visual Studio y el explorador.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-158">Browser Link uses [:::no-loc(SignalR):::](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="c1dcd-159">Si Vínculo con exploradores está habilitado, Visual Studio actuará como un servidor de :::no-loc(SignalR)::: al que se pueden conectar varios clientes (exploradores).</span><span class="sxs-lookup"><span data-stu-id="c1dcd-159">When Browser Link is enabled, Visual Studio acts as a :::no-loc(SignalR)::: server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="c1dcd-160">Vínculo con exploradores también registra un componente de middleware en la canalización de solicitudes de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="c1dcd-161">Este componente inserta referencias especiales de `<script>` en cada solicitud de página del servidor.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="c1dcd-162">Para ver las referencias de script, seleccione **Ver código fuente** en el explorador y desplácese hasta el final del contenido de la etiqueta `<body>`:</span><span class="sxs-lookup"><span data-stu-id="c1dcd-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="c1dcd-163">Los archivos de código fuente no se modifican.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-163">Your source files aren't modified.</span></span> <span data-ttu-id="c1dcd-164">El componente de middleware inserta las referencias de script dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="c1dcd-165">Dado que el código del explorador es JavaScript, funciona en todos los exploradores que admite :::no-loc(SignalR)::: sin requerir un complemento de explorador.</span><span class="sxs-lookup"><span data-stu-id="c1dcd-165">Because the browser-side code is all JavaScript, it works on all browsers that :::no-loc(SignalR)::: supports without requiring a browser plug-in.</span></span>
