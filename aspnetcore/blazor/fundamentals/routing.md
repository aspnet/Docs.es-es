---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo enrutar solicitudes en aplicaciones y sobre el componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: ba85cc901127725d674b699638fef5fe363081a8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402798"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="82d9b-103">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82d9b-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="82d9b-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82d9b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="82d9b-105">Aprenda a enrutar solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="82d9b-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="82d9b-106">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82d9b-106">ASP.NET Core endpoint routing integration</span></span>

Blazor Server<span data-ttu-id="82d9b-107"> se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="82d9b-107"> is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="82d9b-108">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="82d9b-109">La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="82d9b-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="82d9b-110">Convencionalmente, la página del *host* se suele llamar `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="82d9b-111">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="82d9b-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="82d9b-112">y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas.</span><span class="sxs-lookup"><span data-stu-id="82d9b-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="82d9b-113">Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="82d9b-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="82d9b-114">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="82d9b-114">Route templates</span></span>

<span data-ttu-id="82d9b-115">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite el enrutamiento a cada componente con una ruta especificada.</span><span class="sxs-lookup"><span data-stu-id="82d9b-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="82d9b-116">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> aparece en el archivo `App.razor`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="82d9b-117">Cuando se compila un archivo `.razor` con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="82d9b-117">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="82d9b-118">En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:</span><span class="sxs-lookup"><span data-stu-id="82d9b-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="82d9b-119">Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros deseados.</span><span class="sxs-lookup"><span data-stu-id="82d9b-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="82d9b-120">Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.</span><span class="sxs-lookup"><span data-stu-id="82d9b-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="82d9b-121">Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño.</span><span class="sxs-lookup"><span data-stu-id="82d9b-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="82d9b-122">Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="82d9b-123">`MainLayout.razor` está en la carpeta `Shared` del proyecto de plantilla.</span><span class="sxs-lookup"><span data-stu-id="82d9b-123">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="82d9b-124">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="82d9b-125">Se pueden aplicar varias plantillas de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="82d9b-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="82d9b-126">El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="82d9b-127">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo `wwwroot/index.html` (Blazor WebAssembly) o en su archivo `Pages/_Host.cshtml` (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-127">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="82d9b-128">Para obtener más información, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="82d9b-129">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="82d9b-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="82d9b-130">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="82d9b-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="82d9b-131">En el archivo `App.razor`, establezca el contenido personalizado en el parámetro de plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>:</span><span class="sxs-lookup"><span data-stu-id="82d9b-131">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="82d9b-132">El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="82d9b-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="82d9b-133">Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="82d9b-134">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="82d9b-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="82d9b-135">Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="82d9b-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="82d9b-136">Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="82d9b-137">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="82d9b-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="82d9b-138">El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="82d9b-139">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="82d9b-139">Route parameters</span></span>

<span data-ttu-id="82d9b-140">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="82d9b-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="82d9b-141">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="82d9b-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="82d9b-142">En el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="82d9b-143">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="82d9b-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="82d9b-144">mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="82d9b-145">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="82d9b-145">Route constraints</span></span>

<span data-ttu-id="82d9b-146">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="82d9b-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="82d9b-147">En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="82d9b-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="82d9b-148">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="82d9b-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="82d9b-149">El segmento `Id` es un entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="82d9b-150">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="82d9b-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="82d9b-151">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="82d9b-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="82d9b-152">Restricción</span><span class="sxs-lookup"><span data-stu-id="82d9b-152">Constraint</span></span> | <span data-ttu-id="82d9b-153">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="82d9b-153">Example</span></span>           | <span data-ttu-id="82d9b-154">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="82d9b-154">Example Matches</span></span>                                                                  | <span data-ttu-id="82d9b-155">Invariable</span><span class="sxs-lookup"><span data-stu-id="82d9b-155">Invariant</span></span><br><span data-ttu-id="82d9b-156">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="82d9b-156">culture</span></span><br><span data-ttu-id="82d9b-157">coincidencia</span><span class="sxs-lookup"><span data-stu-id="82d9b-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="82d9b-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="82d9b-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="82d9b-159">No</span><span class="sxs-lookup"><span data-stu-id="82d9b-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="82d9b-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="82d9b-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="82d9b-161">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="82d9b-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="82d9b-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="82d9b-163">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="82d9b-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="82d9b-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="82d9b-165">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="82d9b-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="82d9b-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="82d9b-167">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="82d9b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="82d9b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="82d9b-169">No</span><span class="sxs-lookup"><span data-stu-id="82d9b-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="82d9b-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="82d9b-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="82d9b-171">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="82d9b-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="82d9b-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="82d9b-173">Sí</span><span class="sxs-lookup"><span data-stu-id="82d9b-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="82d9b-174">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o <xref:System.DateTime>) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="82d9b-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="82d9b-175">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="82d9b-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="82d9b-176">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="82d9b-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="82d9b-177">En las aplicaciones Blazor Server, la ruta predeterminada en `_Host.cshtml` es `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-177">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="82d9b-178">Una dirección URL de solicitud que contiene un punto (`.`) no coincide con la ruta predeterminada porque la dirección URL parece que solicita un archivo.</span><span class="sxs-lookup"><span data-stu-id="82d9b-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="82d9b-179">Una aplicación Blazor devuelve una respuesta *404 No encontrado* cuando un archivo estático no existe.</span><span class="sxs-lookup"><span data-stu-id="82d9b-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="82d9b-180">Para usar rutas que contienen un punto, configure `_Host.cshtml` con la siguiente plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="82d9b-180">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="82d9b-181">La plantilla `"/{**path}"` incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="82d9b-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="82d9b-182">Una sintaxis de *captura general* de doble asterisco (`**`) para capturar la ruta de acceso en varios límites de carpeta sin codificar las barras diagonales (`/`)</span><span class="sxs-lookup"><span data-stu-id="82d9b-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="82d9b-183">El nombre del parámetro de ruta `path`</span><span class="sxs-lookup"><span data-stu-id="82d9b-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="82d9b-184">La sintaxis de parámetro *Catch-all* (`*`/`**`) **no** se admite en componentes Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="82d9b-185">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="82d9b-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="82d9b-186">NavLink component</span></span>

<span data-ttu-id="82d9b-187">Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="82d9b-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="82d9b-188">Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="82d9b-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="82d9b-189">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="82d9b-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="82d9b-190">El siguiente componente `NavMenu` crea una barra de navegación de [`Bootstrap`](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="82d9b-190">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="82d9b-191">Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="82d9b-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="82d9b-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="82d9b-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="82d9b-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="82d9b-194">En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="82d9b-195">El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="82d9b-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="82d9b-196">Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="82d9b-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="82d9b-197">En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="82d9b-198">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="82d9b-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="82d9b-199">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="82d9b-199">URI and navigation state helpers</span></span>

<span data-ttu-id="82d9b-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="82d9b-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="82d9b-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="82d9b-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="82d9b-202">Miembro</span><span class="sxs-lookup"><span data-stu-id="82d9b-202">Member</span></span> | <span data-ttu-id="82d9b-203">Descripción</span><span class="sxs-lookup"><span data-stu-id="82d9b-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="82d9b-204">Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="82d9b-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="82d9b-205">Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="82d9b-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="82d9b-206">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="82d9b-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="82d9b-207">Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="82d9b-207">Navigates to the specified URI.</span></span> <span data-ttu-id="82d9b-208">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="82d9b-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="82d9b-209">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="82d9b-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="82d9b-210">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="82d9b-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="82d9b-211">Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="82d9b-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="82d9b-212">Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="82d9b-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="82d9b-213">Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base.</span><span class="sxs-lookup"><span data-stu-id="82d9b-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="82d9b-214">El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="82d9b-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="82d9b-215">El componente siguiente controla un evento de cambio de ubicación estableciendo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="82d9b-216">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="82d9b-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="82d9b-217">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="82d9b-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="82d9b-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:</span><span class="sxs-lookup"><span data-stu-id="82d9b-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="82d9b-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="82d9b-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="82d9b-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="82d9b-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="82d9b-221">Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="82d9b-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="82d9b-222">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="82d9b-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
