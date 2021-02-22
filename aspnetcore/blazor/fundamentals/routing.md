---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Aprenda a administrar el enrutamiento de solicitudes en aplicaciones y a usar el componente NavLink en aplicaciones de Blazor para la navegación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279893"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="4f58f-103">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f58f-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="4f58f-104">En este artículo, aprenderá a administrar el enrutamiento de solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4f58f-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="4f58f-105">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="4f58f-105">Route templates</span></span>

<span data-ttu-id="4f58f-106">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> habilita el enrutamiento a componentes Razor en una aplicación de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4f58f-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="4f58f-107">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> se usa en el componente `App` de aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4f58f-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="4f58f-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="4f58f-109">Cuando se compila un componente Razor (`.razor`) con una [directiva `@page`](xref:mvc/views/razor#page), la clase de componente generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute> que especifica la plantilla de ruta del componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="4f58f-110">Cuando se inicia la aplicación, el ensamblado especificado como `AppAssembly` del enrutador se examina para recopilar información de ruta de los componentes de la aplicación que tienen un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="4f58f-111">En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:</span><span class="sxs-lookup"><span data-stu-id="4f58f-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="4f58f-112">Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros de ruta.</span><span class="sxs-lookup"><span data-stu-id="4f58f-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="4f58f-113">Representa el componente especificado con su [diseño](xref:blazor/layouts), incluidos los diseños anidados adicionales.</span><span class="sxs-lookup"><span data-stu-id="4f58f-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="4f58f-114">Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para los componentes que no tengan especificado un diseño con la [directiva `@layout`](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="4f58f-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="4f58f-115">Las plantillas de proyecto Blazor del marco de trabajo especifican el componente `MainLayout` (`Shared/MainLayout.razor`) como diseño predeterminado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4f58f-115">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="4f58f-116">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="4f58f-117">Los componentes admiten varias plantillas de ruta mediante varias [directivas `@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="4f58f-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="4f58f-118">El componente de ejemplo siguiente se carga en las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="4f58f-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="4f58f-120">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo `wwwroot/index.html` (Blazor WebAssembly) o en su archivo `Pages/_Host.cshtml` (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="4f58f-121">Para obtener más información, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="4f58f-122">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="4f58f-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="4f58f-123">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="4f58f-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="4f58f-124">En el componente `App`, establezca el contenido personalizado en la plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="4f58f-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="4f58f-126">Los elementos arbitrarios se admiten como contenido de las etiquetas `<NotFound>`, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="4f58f-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="4f58f-127">Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts#default-layout>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="4f58f-128">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="4f58f-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="4f58f-129">Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="4f58f-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="4f58f-130">Se examinan los ensamblados adicionales, además del ensamblado especificado para `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="4f58f-131">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una [biblioteca de clases de componentes](xref:blazor/components/class-libraries) a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="4f58f-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="4f58f-132">El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="4f58f-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="4f58f-134">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="4f58f-134">Route parameters</span></span>

<span data-ttu-id="4f58f-135">El enrutador usa parámetros de ruta para rellenar los [parámetros de componente](xref:blazor/components/index#component-parameters) correspondientes con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="4f58f-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="4f58f-136">Los nombres de parámetros de ruta no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4f58f-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="4f58f-137">En el ejemplo siguiente, el parámetro `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="4f58f-138">Cuando se realiza una solicitud de `/RouteParameter/amazing`, el contenido de la etiqueta `<h1>` se representa como `Blazor is amazing!`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="4f58f-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4f58f-140">Se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="4f58f-140">Optional parameters are supported.</span></span> <span data-ttu-id="4f58f-141">En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="4f58f-142">Si el segmento no está presente, el valor de `Text` se establece en `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="4f58f-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4f58f-144">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="4f58f-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="4f58f-145">En el ejemplo siguiente, se aplican dos [directivas `@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="4f58f-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="4f58f-146">La primera directiva permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="4f58f-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="4f58f-147">mientras que la segunda directiva asigna el valor del parámetro de ruta `{text}` a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="4f58f-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="4f58f-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set), en lugar de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods), para permitir la navegación de la aplicación al mismo componente con un valor de parámetro opcional diferente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="4f58f-150">Según el ejemplo anterior, use `OnParametersSet` cuando el usuario pueda navegar desde `/RouteParameter` a `/RouteParameter/amazing`, o desde `/RouteParameter/amazing` a `/RouteParameter`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="4f58f-151">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="4f58f-151">Route constraints</span></span>

<span data-ttu-id="4f58f-152">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="4f58f-153">En el siguiente ejemplo, la ruta al componente `User` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="4f58f-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="4f58f-154">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4f58f-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="4f58f-155">El segmento `Id` es un tipo de entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="4f58f-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="4f58f-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="4f58f-157">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="4f58f-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="4f58f-158">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, consulte la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="4f58f-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="4f58f-159">Restricción</span><span class="sxs-lookup"><span data-stu-id="4f58f-159">Constraint</span></span> | <span data-ttu-id="4f58f-160">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="4f58f-160">Example</span></span>           | <span data-ttu-id="4f58f-161">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="4f58f-161">Example Matches</span></span>                                                                  | <span data-ttu-id="4f58f-162">Invariable</span><span class="sxs-lookup"><span data-stu-id="4f58f-162">Invariant</span></span><br><span data-ttu-id="4f58f-163">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="4f58f-163">culture</span></span><br><span data-ttu-id="4f58f-164">coincidencia</span><span class="sxs-lookup"><span data-stu-id="4f58f-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="4f58f-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="4f58f-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="4f58f-166">No</span><span class="sxs-lookup"><span data-stu-id="4f58f-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="4f58f-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="4f58f-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="4f58f-168">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="4f58f-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="4f58f-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="4f58f-170">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="4f58f-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="4f58f-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="4f58f-172">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="4f58f-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="4f58f-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="4f58f-174">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="4f58f-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="4f58f-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="4f58f-176">No</span><span class="sxs-lookup"><span data-stu-id="4f58f-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="4f58f-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="4f58f-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="4f58f-178">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="4f58f-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="4f58f-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="4f58f-180">Sí</span><span class="sxs-lookup"><span data-stu-id="4f58f-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="4f58f-181">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o <xref:System.DateTime>) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="4f58f-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="4f58f-182">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="4f58f-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="4f58f-183">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="4f58f-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="4f58f-184">En el caso de las aplicaciones hospedadas de Blazor WebAssembly y Blazor Server, la plantilla de ruta predeterminada del lado servidor presupone que, si el último segmento de una dirección URL de solicitud contiene un punto (`.`), se solicita un archivo.</span><span class="sxs-lookup"><span data-stu-id="4f58f-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="4f58f-185">Por ejemplo, el enrutador interpreta la dirección URL `https://localhost.com:5001/example/some.thing` como una solicitud de un archivo denominado `some.thing`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="4f58f-186">Sin configuración adicional, una aplicación devuelve una respuesta *404: no encontrado* si se pretendía enrutar `some.thing` a un componente con una [directiva `@page`](xref:mvc/views/razor#page) y `some.thing` es un valor de parámetro de ruta.</span><span class="sxs-lookup"><span data-stu-id="4f58f-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="4f58f-187">Para usar una ruta con uno o más parámetros que contengan un punto, la aplicación debe configurar la ruta con una plantilla personalizada.</span><span class="sxs-lookup"><span data-stu-id="4f58f-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="4f58f-188">Tenga en cuenta el siguiente componente `Example` que puede recibir un parámetro de ruta del último segmento de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4f58f-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="4f58f-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="4f58f-190">Para permitir que la aplicación *`Server`* de una solución de Blazor WebAssembly hospedada enrute la solicitud con un punto en el parámetro `param`, agregue una plantilla de ruta de archivo de reserva con el parámetro opcional en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-190">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="4f58f-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="4f58f-192">Para configurar una aplicación Blazor Server para enrutar la solicitud con un punto en el parámetro de ruta `param`, agregue una plantilla de ruta de página de reserva con el parámetro opcional en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="4f58f-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="4f58f-194">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="4f58f-195">Parámetros de ruta de captura general</span><span class="sxs-lookup"><span data-stu-id="4f58f-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4f58f-196">Los parámetros de ruta de captura general, que capturan rutas de acceso en varios límites de carpeta, se admiten en los componentes.</span><span class="sxs-lookup"><span data-stu-id="4f58f-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="4f58f-197">Los parámetros de ruta de captura general son:</span><span class="sxs-lookup"><span data-stu-id="4f58f-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="4f58f-198">Con nombre para que coincida con el nombre del segmento de ruta.</span><span class="sxs-lookup"><span data-stu-id="4f58f-198">Named to match the route segment name.</span></span> <span data-ttu-id="4f58f-199">La nomenclatura no distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4f58f-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="4f58f-200">Tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-200">A `string` type.</span></span> <span data-ttu-id="4f58f-201">El marco no proporciona conversión automática.</span><span class="sxs-lookup"><span data-stu-id="4f58f-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="4f58f-202">Al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4f58f-202">At the end of the URL.</span></span>

<span data-ttu-id="4f58f-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="4f58f-204">Para la dirección URL `/catch-all/this/is/a/test` con una plantilla de ruta de `/catch-all/{*pageRoute}`, el valor de `PageRoute` se establece en `this/is/a/test`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="4f58f-205">Las barras diagonales y los segmentos de la ruta de acceso capturada están descodificados.</span><span class="sxs-lookup"><span data-stu-id="4f58f-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="4f58f-206">En el caso de una plantilla de ruta de `/catch-all/{*pageRoute}`, la dirección URL `/catch-all/this/is/a%2Ftest%2A` produce `this/is/a/test*`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4f58f-207">Los parámetros de ruta de captura general se admiten en ASP.NET Core 5.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="4f58f-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="4f58f-208">Para más información, seleccione la versión 5.0 de este artículo.</span><span class="sxs-lookup"><span data-stu-id="4f58f-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="4f58f-209">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="4f58f-209">URI and navigation state helpers</span></span>

<span data-ttu-id="4f58f-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para administrar los URI y la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="4f58f-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="4f58f-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="4f58f-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="4f58f-212">Miembro</span><span class="sxs-lookup"><span data-stu-id="4f58f-212">Member</span></span> | <span data-ttu-id="4f58f-213">Descripción</span><span class="sxs-lookup"><span data-stu-id="4f58f-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="4f58f-214">Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="4f58f-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="4f58f-215">Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="4f58f-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="4f58f-216">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="4f58f-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="4f58f-217">Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="4f58f-217">Navigates to the specified URI.</span></span> <span data-ttu-id="4f58f-218">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="4f58f-219">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="4f58f-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="4f58f-220">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="4f58f-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="4f58f-221">Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="4f58f-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="4f58f-222">Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="4f58f-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="4f58f-223">Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base.</span><span class="sxs-lookup"><span data-stu-id="4f58f-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="4f58f-224">En el caso del evento <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged>, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre los eventos de navegación:</span><span class="sxs-lookup"><span data-stu-id="4f58f-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="4f58f-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="4f58f-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="4f58f-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="4f58f-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="4f58f-227">Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="4f58f-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="4f58f-228">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="4f58f-228">The following component:</span></span>

* <span data-ttu-id="4f58f-229">Navega al componente `Counter` de la aplicación (`Pages/Counter.razor`) cuando se selecciona el botón con <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="4f58f-230">Controla el evento de ubicación cambiado mediante la suscripción a <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="4f58f-231">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="4f58f-232">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="4f58f-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="4f58f-233">La implementación del registrador registra la siguiente información cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="4f58f-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="4f58f-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="4f58f-235">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="4f58f-236">Parámetros de cadena de consulta y de análisis</span><span class="sxs-lookup"><span data-stu-id="4f58f-236">Query string and parse parameters</span></span>

<span data-ttu-id="4f58f-237">La cadena de consulta de una solicitud se obtiene de la propiedad <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="4f58f-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="4f58f-238">Para analizar los parámetros de una cadena de consulta:</span><span class="sxs-lookup"><span data-stu-id="4f58f-238">To parse a query string's parameters:</span></span>

* <span data-ttu-id="4f58f-239">Una aplicación puede usar la API <xref:Microsoft.AspNetCore.WebUtilities>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-239">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="4f58f-240">Si la API no está disponible para la aplicación, agregue una referencia de paquete en el archivo de proyecto de la aplicación para [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="4f58f-240">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="4f58f-241">Obtenga el valor después de analizar la cadena de consulta con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-241">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="4f58f-242">En el siguiente ejemplo del componente `ParseQueryString` se analiza una clave de parámetro de cadena de consulta denominada `ship`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-242">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="4f58f-243">Por ejemplo, el par clave-valor de la cadena de consulta de dirección URL `?ship=Tardis` captura el valor `Tardis` en `queryValue`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-243">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="4f58f-244">En el ejemplo siguiente, vaya a la aplicación con la dirección URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-244">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="4f58f-245">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-245">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="4f58f-246">componentes `NavLink` y `NavMenu`</span><span class="sxs-lookup"><span data-stu-id="4f58f-246">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="4f58f-247">Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="4f58f-247">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="4f58f-248">Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="4f58f-248">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="4f58f-249">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="4f58f-249">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="4f58f-250">Opcionalmente, asigne un nombre de clase CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar una clase CSS personalizada al vínculo representado cuando la ruta actual coincida con `href`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-250">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="4f58f-251">El siguiente componente `NavMenu` crea una barra de navegación de [`Bootstrap`](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="4f58f-251">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="4f58f-252">El componente `NavMenu` (`NavMenu.razor`) se proporciona en la carpeta `Shared` de una aplicación generada a partir de las plantillas de proyecto de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4f58f-252">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="4f58f-253">Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-253">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="4f58f-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="4f58f-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="4f58f-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="4f58f-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="4f58f-256">En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="4f58f-256">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="4f58f-257">El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `component` (por ejemplo, `https://localhost:5001/component` y `https://localhost:5001/component/another-segment`).</span><span class="sxs-lookup"><span data-stu-id="4f58f-257">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="4f58f-258">Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="4f58f-258">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="4f58f-259">En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-259">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="4f58f-260">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="4f58f-260">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="4f58f-261">Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes `NavLink` dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente `NavLink` (secundario):</span><span class="sxs-lookup"><span data-stu-id="4f58f-261">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="4f58f-262">El uso de una variable de índice en este escenario es un requisito para **cualquier** componente secundario que use una variable de bucle en su [contenido secundario](xref:blazor/components/index#child-content), no solo para el componente `NavLink`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-262">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="4f58f-263">También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="4f58f-263">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="4f58f-264">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f58f-264">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="4f58f-265">*Esta sección solo se aplica a las aplicaciones Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="4f58f-265">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="4f58f-266">Blazor Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="4f58f-266">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="4f58f-267">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4f58f-267">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="4f58f-268">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="4f58f-268">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="4f58f-269">La configuración típica consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4f58f-269">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="4f58f-270">Convencionalmente, la página del *host* se suele llamar `_Host.cshtml`en la carpeta `Pages` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4f58f-270">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="4f58f-271">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="4f58f-271">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="4f58f-272">y se solo se usa cuando no se encuentran coincidencias con otras rutas.</span><span class="sxs-lookup"><span data-stu-id="4f58f-272">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="4f58f-273">Esto permite a la aplicación usar otros controladores y páginas sin interferir con el enrutamiento de componentes en la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4f58f-273">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="4f58f-274">Para información sobre cómo configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> para el hospedaje de servidores de direcciones URL no raíz, consulte <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="4f58f-274">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
