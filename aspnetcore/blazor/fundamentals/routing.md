---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo enrutar solicitudes en aplicaciones y sobre el componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: c4da8bf8447618c9a7a2d0f690164fe48a7ed006
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703701"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="6c098-103">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c098-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="6c098-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6c098-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6c098-105">Aprenda a enrutar solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="6c098-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="6c098-106">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c098-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="6c098-107">Blazor Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="6c098-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6c098-108">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6c098-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6c098-109">La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6c098-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="6c098-110">Convencionalmente, la página del *host* se suele llamar `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="6c098-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="6c098-111">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="6c098-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="6c098-112">y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas.</span><span class="sxs-lookup"><span data-stu-id="6c098-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="6c098-113">Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6c098-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="6c098-114">Para información sobre cómo configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> para el hospedaje de servidores de direcciones URL no raíz, consulte <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6c098-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="6c098-115">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="6c098-115">Route templates</span></span>

<span data-ttu-id="6c098-116">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite el enrutamiento a cada componente con una ruta especificada.</span><span class="sxs-lookup"><span data-stu-id="6c098-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="6c098-117">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> aparece en el archivo `App.razor`:</span><span class="sxs-lookup"><span data-stu-id="6c098-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="6c098-118">Cuando se compila un archivo `.razor` con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="6c098-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="6c098-119">En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:</span><span class="sxs-lookup"><span data-stu-id="6c098-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="6c098-120">Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros deseados.</span><span class="sxs-lookup"><span data-stu-id="6c098-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="6c098-121">Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.</span><span class="sxs-lookup"><span data-stu-id="6c098-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="6c098-122">Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño.</span><span class="sxs-lookup"><span data-stu-id="6c098-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="6c098-123">Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="6c098-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="6c098-124">`MainLayout.razor` está en la carpeta `Shared` del proyecto de plantilla.</span><span class="sxs-lookup"><span data-stu-id="6c098-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="6c098-125">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6c098-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="6c098-126">Se pueden aplicar varias plantillas de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="6c098-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="6c098-127">El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="6c098-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="6c098-128">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo `wwwroot/index.html` (Blazor WebAssembly) o en su archivo `Pages/_Host.cshtml` (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="6c098-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="6c098-129">Para obtener más información, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="6c098-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="6c098-130">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="6c098-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="6c098-131">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="6c098-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="6c098-132">En el archivo `App.razor`, establezca el contenido personalizado en el parámetro de plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>:</span><span class="sxs-lookup"><span data-stu-id="6c098-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="6c098-133">El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="6c098-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="6c098-134">Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="6c098-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="6c098-135">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="6c098-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="6c098-136">Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="6c098-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="6c098-137">Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="6c098-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="6c098-138">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="6c098-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="6c098-139">El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`:</span><span class="sxs-lookup"><span data-stu-id="6c098-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="6c098-140">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="6c098-140">Route parameters</span></span>

<span data-ttu-id="6c098-141">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas).</span><span class="sxs-lookup"><span data-stu-id="6c098-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6c098-142">Se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="6c098-142">Optional parameters are supported.</span></span> <span data-ttu-id="6c098-143">En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="6c098-143">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="6c098-144">Si el segmento no está presente, el valor de `Text` se establece en `fantastic`:</span><span class="sxs-lookup"><span data-stu-id="6c098-144">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="6c098-145">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="6c098-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="6c098-146">En el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="6c098-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="6c098-147">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="6c098-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6c098-148">mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="6c098-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="6c098-149">Úsela en [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set), en lugar de en [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods), para permitir la navegación de la aplicación al mismo componente con un valor de parámetro opcional diferente.</span><span class="sxs-lookup"><span data-stu-id="6c098-149">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="6c098-150">Según el ejemplo anterior, use `OnParametersSet` cuando el usuario pueda navegar desde `/RouteParameter` a `/RouteParameter/awesome`, o desde `/RouteParameter/awesome` a `/RouteParameter`:</span><span class="sxs-lookup"><span data-stu-id="6c098-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="6c098-151">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="6c098-151">Route constraints</span></span>

<span data-ttu-id="6c098-152">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="6c098-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="6c098-153">En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="6c098-153">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="6c098-154">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6c098-154">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="6c098-155">El segmento `Id` es un entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="6c098-155">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="6c098-156">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="6c098-156">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="6c098-157">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="6c098-157">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="6c098-158">Restricción</span><span class="sxs-lookup"><span data-stu-id="6c098-158">Constraint</span></span> | <span data-ttu-id="6c098-159">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="6c098-159">Example</span></span>           | <span data-ttu-id="6c098-160">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="6c098-160">Example Matches</span></span>                                                                  | <span data-ttu-id="6c098-161">Invariable</span><span class="sxs-lookup"><span data-stu-id="6c098-161">Invariant</span></span><br><span data-ttu-id="6c098-162">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="6c098-162">culture</span></span><br><span data-ttu-id="6c098-163">coincidencia</span><span class="sxs-lookup"><span data-stu-id="6c098-163">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="6c098-164">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="6c098-164">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="6c098-165">No</span><span class="sxs-lookup"><span data-stu-id="6c098-165">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="6c098-166">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="6c098-166">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="6c098-167">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-167">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="6c098-168">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="6c098-168">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="6c098-169">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-169">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="6c098-170">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6c098-170">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6c098-171">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-171">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="6c098-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="6c098-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="6c098-173">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-173">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="6c098-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="6c098-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="6c098-175">No</span><span class="sxs-lookup"><span data-stu-id="6c098-175">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="6c098-176">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6c098-176">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6c098-177">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-177">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="6c098-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="6c098-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="6c098-179">Sí</span><span class="sxs-lookup"><span data-stu-id="6c098-179">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="6c098-180">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o <xref:System.DateTime>) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="6c098-180">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="6c098-181">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="6c098-181">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="6c098-182">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="6c098-182">Routing with URLs that contain dots</span></span>

<span data-ttu-id="6c098-183">En el caso de las aplicaciones hospedadas de Blazor WebAssembly y Blazor Server, la plantilla de ruta predeterminada del lado servidor presupone que, si el último segmento de una dirección URL de solicitud contiene un punto (`.`), se solicita un archivo (por ejemplo, `https://localhost.com:5001/example/some.thing`).</span><span class="sxs-lookup"><span data-stu-id="6c098-183">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="6c098-184">Sin configuración adicional, una aplicación devuelve una respuesta *404: no encontrado* si se pretendía enrutar a un componente.</span><span class="sxs-lookup"><span data-stu-id="6c098-184">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="6c098-185">Para usar una ruta con uno o más parámetros que contengan un punto, la aplicación debe configurar la ruta con una plantilla personalizada.</span><span class="sxs-lookup"><span data-stu-id="6c098-185">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="6c098-186">Tenga en cuenta el siguiente componente de `Example` que puede recibir un parámetro de ruta del último segmento de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="6c098-186">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="6c098-187">Para permitir que la aplicación *Server* de una solución de Blazor WebAssembly hospedada enrute la solicitud con un punto en el parámetro `param`, agregue una plantilla de ruta de archivo de reserva con el parámetro opcional en `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="6c098-187">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="6c098-188">Para configurar una aplicación Blazor Server para enrutar la solicitud con un punto en el parámetro `param`+, agregue una plantilla de ruta de página de reserva con el parámetro opcional en `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="6c098-188">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="6c098-189">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="6c098-189">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="6c098-190">Parámetros de ruta de captura general</span><span class="sxs-lookup"><span data-stu-id="6c098-190">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6c098-191">*Esta sección se aplica a ASP.NET Core en la versión candidata para lanzamiento 1 (RC1) de .NET 5 o una posterior.*</span><span class="sxs-lookup"><span data-stu-id="6c098-191">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="6c098-192">Los parámetros de ruta de captura general, que capturan rutas de acceso en varios límites de carpeta, se admiten en los componentes.</span><span class="sxs-lookup"><span data-stu-id="6c098-192">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="6c098-193">El parámetro de ruta de captura general debe ser:</span><span class="sxs-lookup"><span data-stu-id="6c098-193">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="6c098-194">Con nombre para que coincida con el nombre del segmento de ruta.</span><span class="sxs-lookup"><span data-stu-id="6c098-194">Named to match the route segment name.</span></span> <span data-ttu-id="6c098-195">La nomenclatura no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6c098-195">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="6c098-196">Tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="6c098-196">A `string` type.</span></span> <span data-ttu-id="6c098-197">El marco no proporciona conversión automática.</span><span class="sxs-lookup"><span data-stu-id="6c098-197">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="6c098-198">Al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="6c098-198">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="6c098-199">Para la dirección URL `/page/this/is/a/test` con una plantilla de ruta de `/page/{*pageRoute}`, el valor de `PageRoute` se establece en `this/is/a/test`.</span><span class="sxs-lookup"><span data-stu-id="6c098-199">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="6c098-200">Las barras diagonales y los segmentos de la ruta de acceso capturada están descodificados.</span><span class="sxs-lookup"><span data-stu-id="6c098-200">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="6c098-201">En el caso de una plantilla de ruta de `/page/{*pageRoute}`, la dirección URL `/page/this/is/a%2Ftest%2A` produce `this/is/a/test*`.</span><span class="sxs-lookup"><span data-stu-id="6c098-201">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6c098-202">Los parámetros de ruta de captura general se admiten en ASP.NET Core 5.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="6c098-202">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="6c098-203">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="6c098-203">NavLink component</span></span>

<span data-ttu-id="6c098-204">Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="6c098-204">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="6c098-205">Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="6c098-205">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="6c098-206">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="6c098-206">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="6c098-207">Opcionalmente, asigne un nombre de clase CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar una clase CSS personalizada al vínculo representado cuando la ruta actual coincida con `href`.</span><span class="sxs-lookup"><span data-stu-id="6c098-207">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="6c098-208">El siguiente componente `NavMenu` crea una barra de navegación de [`Bootstrap`](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="6c098-208">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="6c098-209">Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="6c098-209">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="6c098-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="6c098-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="6c098-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="6c098-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="6c098-212">En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="6c098-212">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="6c098-213">El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="6c098-213">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="6c098-214">Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="6c098-214">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="6c098-215">En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="6c098-215">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="6c098-216">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="6c098-216">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="6c098-217">Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes `NavLink` dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente `NavLink` (secundario):</span><span class="sxs-lookup"><span data-stu-id="6c098-217">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="6c098-218">El uso de una variable de índice en este escenario es un requisito para **cualquier** componente secundario que use una variable de bucle en su [contenido secundario](xref:blazor/components/index#child-content), no solo para el componente `NavLink`.</span><span class="sxs-lookup"><span data-stu-id="6c098-218">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="6c098-219">También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="6c098-219">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="6c098-220">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="6c098-220">URI and navigation state helpers</span></span>

<span data-ttu-id="6c098-221">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="6c098-221">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="6c098-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="6c098-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="6c098-223">Miembro</span><span class="sxs-lookup"><span data-stu-id="6c098-223">Member</span></span> | <span data-ttu-id="6c098-224">Descripción</span><span class="sxs-lookup"><span data-stu-id="6c098-224">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="6c098-225">Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="6c098-225">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="6c098-226">Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="6c098-226">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="6c098-227">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="6c098-227">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="6c098-228">Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="6c098-228">Navigates to the specified URI.</span></span> <span data-ttu-id="6c098-229">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="6c098-229">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="6c098-230">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="6c098-230">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="6c098-231">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="6c098-231">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="6c098-232">Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="6c098-232">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="6c098-233">Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="6c098-233">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="6c098-234">Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base.</span><span class="sxs-lookup"><span data-stu-id="6c098-234">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="6c098-235">El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="6c098-235">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="6c098-236">El componente siguiente controla un evento de cambio de ubicación suscribiéndose a <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6c098-236">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6c098-237">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="6c098-237">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="6c098-238">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="6c098-238">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="6c098-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:</span><span class="sxs-lookup"><span data-stu-id="6c098-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="6c098-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="6c098-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="6c098-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="6c098-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="6c098-242">Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="6c098-242">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="6c098-243">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6c098-243">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="6c098-244">Parámetros de cadena de consulta y de análisis</span><span class="sxs-lookup"><span data-stu-id="6c098-244">Query string and parse parameters</span></span>

<span data-ttu-id="6c098-245">La cadena de consulta de una solicitud se puede obtener de la propiedad <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> de <xref:Microsoft.AspNetCore.Components.NavigationManager>:</span><span class="sxs-lookup"><span data-stu-id="6c098-245">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="6c098-246">Para analizar los parámetros de una cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="6c098-246">To parse a query string's parameters:</span></span>

* <span data-ttu-id="6c098-247">Agregue una referencia de paquete para [Microsoft.AspNetCore.webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="6c098-247">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="6c098-248">Obtenga el valor después de analizar la cadena de consulta con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="6c098-248">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="6c098-249">El marcador de posición `{KEY}` del ejemplo anterior es la clave del parámetro de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="6c098-249">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="6c098-250">Por ejemplo, el par clave-valor de dirección URL `?ship=Tardis` utiliza una clave de `ship`.</span><span class="sxs-lookup"><span data-stu-id="6c098-250">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
