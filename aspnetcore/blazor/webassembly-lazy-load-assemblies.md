---
title: Ensamblados de carga diferida en Blazor WebAssembly de ASP.NET Core
author: guardrex
description: Descubra cómo cargar ensamblados de forma diferida en aplicaciones de Blazor WebAssembly de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0fb744b4e9d44e6b8136123fddfb75ace8901d52
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819933"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="ca2b6-103">Ensamblados de carga diferida en Blazor WebAssembly de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca2b6-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ca2b6-104">Por [Safia Abdalla](https://safia.rocks) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ca2b6-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ca2b6-105">El rendimiento de inicio de la aplicación Blazor WebAssembly se puede mejorar aplazando la carga de algunos ensamblados de la aplicación hasta que sean necesarios, lo que se denomina *carga diferida*.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="ca2b6-106">Por ejemplo, los ensamblados que solo se usan para representar un único componente se pueden configurar para que se carguen solo si el usuario navega a ese componente.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="ca2b6-107">Después de la carga, los ensamblados se almacenan en la memoria caché del lado cliente y están disponibles para todas las navegaciones futuras.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="ca2b6-108">La característica de carga diferida de Blazor permite marcar los ensamblados de la aplicación para la carga diferida, que carga los ensamblados durante el tiempo de ejecución cuando el usuario navega a una ruta determinada.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="ca2b6-109">La característica consiste en cambios en el archivo del proyecto y en el enrutador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2b6-110">La carga diferida de ensamblados no se beneficia de las aplicaciones de Blazor Server porque los ensamblados no se descargan en el cliente en una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="ca2b6-111">Archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="ca2b6-111">Project file</span></span>

<span data-ttu-id="ca2b6-112">Marque los ensamblados para la carga diferida en el archivo del proyecto de la aplicación (`.csproj`) mediante el elemento `BlazorWebAssemblyLazyLoad`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="ca2b6-113">Use el nombre de ensamblado sin la extensión `.dll`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="ca2b6-114">El marco de trabajo de Blazor impide que los ensamblados especificados por este grupo de elementos se carguen al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="ca2b6-115">En el ejemplo siguiente se marca un ensamblado personalizado grande (`GrantImaharaRobotControls.dll`) para la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="ca2b6-116">Si un ensamblado que está marcado para la carga diferida tiene dependencias, también se deben marcar para la carga diferida en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="ca2b6-117">Solo se pueden cargar de forma diferida los ensamblados utilizados por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="ca2b6-118">El enlazador quita los ensamblados que no se usan de la salida publicada.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="ca2b6-119">Componente de `Router`</span><span class="sxs-lookup"><span data-stu-id="ca2b6-119">`Router` component</span></span>

<span data-ttu-id="ca2b6-120">El componente `Router` de Blazor designa qué ensamblados busca Blazor para los componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="ca2b6-121">El componente `Router` también es responsable de representar el componente para la ruta donde navega el usuario.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="ca2b6-122">El componente `Router` admite una característica `OnNavigateAsync` que se puede usar junto con la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="ca2b6-123">En el componente `Router` de la aplicación (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="ca2b6-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="ca2b6-124">Agregue una devolución de llamada `OnNavigateAsync`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="ca2b6-125">El controlador `OnNavigateAsync` se invoca cuando el usuario:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="ca2b6-126">Visita una ruta por primera vez desplazándose hasta ella directamente desde el explorador.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="ca2b6-127">Navega a una nueva ruta mediante un vínculo o una invocación de <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="ca2b6-128">Si los ensamblados de carga diferida contienen componentes enrutables, agregue un elemento [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (por ejemplo, con el nombre `lazyLoadedAssemblies`) al componente.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="ca2b6-129">Los ensamblados se devuelven a la colección <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> en caso de que los ensamblados contengan componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="ca2b6-130">El marco de trabajo busca las rutas en los ensamblados y actualiza la colección de rutas si se encuentran nuevas rutas.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="ca2b6-131">Si la devolución de llamada de `OnNavigateAsync` produce una excepción no controlada, se invoca la [interfaz de usuario de error Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="ca2b6-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="ca2b6-132">Lógica de carga de ensamblado en `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="ca2b6-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="ca2b6-133">`OnNavigateAsync` tiene un parámetro `NavigationContext` que proporciona información sobre el evento de navegación asincrónico actual, incluida la ruta de acceso de destino (`Path`) y el token de cancelación (`CancellationToken`):</span><span class="sxs-lookup"><span data-stu-id="ca2b6-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="ca2b6-134">La propiedad `Path` es la ruta de acceso de destino del usuario en relación con la ruta de acceso base de la aplicación, como `/robot`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="ca2b6-135">`CancellationToken` se puede usar para observar la cancelación de la tarea asincrónica.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="ca2b6-136">`OnNavigateAsync` cancela automáticamente la tarea de navegación actualmente en ejecución cuando el usuario navega a una página diferente.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="ca2b6-137">Dentro de `OnNavigateAsync`, implemente la lógica para determinar los ensamblados que se van a cargar.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="ca2b6-138">Las opciones incluyen:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-138">Options include:</span></span>

* <span data-ttu-id="ca2b6-139">Comprobaciones condicionales dentro del método `OnNavigateAsync`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="ca2b6-140">Tabla de búsqueda que asigna rutas a los nombres de ensamblado, insertados en el componente o implementados en el bloque [`@code`](xref:mvc/views/razor#code).</span><span class="sxs-lookup"><span data-stu-id="ca2b6-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="ca2b6-141">`LazyAssemblyLoader` es un servicio singleton proporcionado por el marco de trabajo para cargar ensamblados.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="ca2b6-142">Inserte `LazyAssemblyLoader` en el componente `Router`:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="ca2b6-143">`LazyAssemblyLoader` proporciona el método `LoadAssembliesAsync` que:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="ca2b6-144">Usa la interoperabilidad de JS para capturar ensamblados a través de una llamada de red.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="ca2b6-145">Carga los ensamblados en el runtime que se ejecuta en WebAssembly en el explorador.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2b6-146">La implementación de la carga diferida del marco de trabajo admite la representación previa en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="ca2b6-147">Durante la representación previa, se supone que se cargan todos los ensamblados, incluidos los marcados para la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="ca2b6-148">Interacción del usuario con el contenido de `<Navigating>`</span><span class="sxs-lookup"><span data-stu-id="ca2b6-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="ca2b6-149">Mientras se cargan los ensamblados, que pueden tardar varios segundos, el componente `Router` puede indicar al usuario que se está produciendo una transición de página:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="ca2b6-150">Agregue una directiva [`@using`](xref:mvc/views/razor#using) para el espacio de nombres <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="ca2b6-151">Agregue una etiqueta `<Navigating>` al componente con el marcado que se va a mostrar durante los eventos de transición de la página.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="ca2b6-152">Control de las cancelaciones en `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="ca2b6-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="ca2b6-153">El objeto `NavigationContext` pasado a la devolución de llamada de `OnNavigateAsync` contiene un elemento `CancellationToken` que se establece cuando se produce un nuevo evento de navegación.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="ca2b6-154">La devolución de llamada de `OnNavigateAsync` debe iniciarse cuando se establece este token de cancelación para evitar que continúe la ejecución de la devolución de llamada de `OnNavigateAsync` en una navegación no actualizada.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="ca2b6-155">Si un usuario navega a la ruta A e inmediatamente después a la ruta B, la aplicación no debe seguir ejecutando la devolución de llamada de `OnNavigateAsync` para la ruta A:</span><span class="sxs-lookup"><span data-stu-id="ca2b6-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ca2b6-156">Cuando no se ejecuta si el token de cancelación de `NavigationContext` se cancela, puede resultar en un comportamiento imprevisto, como la representación de un componente de una navegación anterior.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="ca2b6-157">Ejemplo completo</span><span class="sxs-lookup"><span data-stu-id="ca2b6-157">Complete example</span></span>

<span data-ttu-id="ca2b6-158">El siguiente componente `Router` completo muestra cómo cargar el ensamblado `GrantImaharaRobotControls.dll` cuando el usuario navega a `/robot`.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="ca2b6-159">Durante las transiciones de página, se muestra al usuario un mensaje con estilo.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="ca2b6-160">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="ca2b6-160">Troubleshoot</span></span>

* <span data-ttu-id="ca2b6-161">Si se produce una representación inesperada (por ejemplo, se representa un componente de una navegación anterior), confirme que el código se ejecuta si se establece el token de cancelación.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="ca2b6-162">Si los ensamblados continúan cargándose cuando se inicia la aplicación, compruebe que el ensamblado está marcado como cargado de forma diferida en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ca2b6-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca2b6-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ca2b6-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
