---
title: Ciclo de vida de ASP.NET Core Blazor
author: guardrex
description: Aprenda a usar los métodos de ciclo de vida de los componentes de Razor en aplicaciones de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 08fc393160e0a7396963901e2add3b44fc7b02b9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508032"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="861fe-103">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="861fe-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="861fe-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="861fe-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="861fe-105">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="861fe-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="861fe-106">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="861fe-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="861fe-107">Los siguientes diagramas ilustran el ciclo de vida de Blazor.</span><span class="sxs-lookup"><span data-stu-id="861fe-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="861fe-108">Los métodos de ciclo de vida se definen con ejemplos en las siguientes secciones de este artículo.</span><span class="sxs-lookup"><span data-stu-id="861fe-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="861fe-109">Eventos del ciclo de vida del componente:</span><span class="sxs-lookup"><span data-stu-id="861fe-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="861fe-110">Si el componente se representa por primera vez en una solicitud:</span><span class="sxs-lookup"><span data-stu-id="861fe-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="861fe-111">Cree la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-111">Create the component's instance.</span></span>
   * <span data-ttu-id="861fe-112">Realice la inserción de propiedades.</span><span class="sxs-lookup"><span data-stu-id="861fe-112">Perform property injection.</span></span> <span data-ttu-id="861fe-113">Ejecute [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="861fe-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="861fe-114">Llame a [`OnInitialized{Async}`](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="861fe-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="861fe-115">Si se devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y, a continuación, se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="861fe-116">Si no se devuelve <xref:System.Threading.Tasks.Task>, represente el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="861fe-117">Llame a [`OnParametersSet{Async}`](#after-parameters-are-set) y represente el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="861fe-118">Si `OnParametersSetAsync` devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y, a continuación, se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Eventos de ciclo de vida de componentes de un componente Razor en Blazor](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="861fe-120">Procesamiento de eventos de Document Object Model (DOM):</span><span class="sxs-lookup"><span data-stu-id="861fe-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="861fe-121">Se ejecuta el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="861fe-121">The event handler is run.</span></span>
1. <span data-ttu-id="861fe-122">Si se devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y, a continuación, se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="861fe-123">Si no se devuelve <xref:System.Threading.Tasks.Task>, se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Procesamiento de eventos de Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="861fe-125">Ciclo de vida de `Render`:</span><span class="sxs-lookup"><span data-stu-id="861fe-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="861fe-126">Si no es la primera representación del componente o si se evalúa [`ShouldRender`](#suppress-ui-refreshing) como `false`, no realice más operaciones en el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-126">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="861fe-127">Compile la diff (comparación) del árbol de representación y represente el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-127">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="861fe-128">Espere a que se actualice DOM.</span><span class="sxs-lookup"><span data-stu-id="861fe-128">Await the DOM to update.</span></span>
1. <span data-ttu-id="861fe-129">Llame a [`OnAfterRender{Async}`](#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="861fe-129">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Ciclo de vida de representación](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="861fe-131">Las llamadas del desarrollador a [`StateHasChanged`](#state-changes) producen una representación.</span><span class="sxs-lookup"><span data-stu-id="861fe-131">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="861fe-132">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="861fe-132">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="861fe-133">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="861fe-133">Before parameters are set</span></span>

<span data-ttu-id="861fe-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:</span><span class="sxs-lookup"><span data-stu-id="861fe-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="861fe-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene el conjunto de valores de parámetros del componente cada vez que se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="861fe-136">La implementación predeterminada de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece el valor de cada propiedad con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), que tiene un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="861fe-136">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="861fe-137">Los parámetros que no tienen un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView> se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="861fe-137">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="861fe-138">Si no se invoca [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A), el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="861fe-138">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="861fe-139">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="861fe-139">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="861fe-140">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="861fe-140">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="861fe-141">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="861fe-141">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="861fe-142">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="861fe-142">Component initialization methods</span></span>

<span data-ttu-id="861fe-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario en <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="861fe-144">Utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="861fe-144">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="861fe-145">En el caso de una operación sincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="861fe-145">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="861fe-146">Para realizar una operación asincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y use el operador [`await`](/dotnet/csharp/language-reference/operators/await) en la operación:</span><span class="sxs-lookup"><span data-stu-id="861fe-146">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="861fe-147">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/fundamentals/additional-scenarios#render-mode) llaman a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dos veces_**:</span><span class="sxs-lookup"><span data-stu-id="861fe-147">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="861fe-148">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="861fe-148">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="861fe-149">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="861fe-149">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="861fe-150">Para evitar que el código de desarrollador en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="861fe-150">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="861fe-151">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="861fe-151">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="861fe-152">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="861fe-152">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="861fe-153">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="861fe-153">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="861fe-154">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="861fe-154">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="861fe-155">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="861fe-155">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="861fe-156">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="861fe-156">After parameters are set</span></span>

<span data-ttu-id="861fe-157">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:</span><span class="sxs-lookup"><span data-stu-id="861fe-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="861fe-158">Después de inicializar el componente en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-158">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="861fe-159">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="861fe-159">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="861fe-160">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="861fe-160">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="861fe-161">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="861fe-161">Any complex-typed parameters.</span></span> <span data-ttu-id="861fe-162">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="861fe-162">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="861fe-163">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-163">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="861fe-164">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="861fe-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="861fe-165">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="861fe-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="861fe-166">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="861fe-166">After component render</span></span>

<span data-ttu-id="861fe-167">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="861fe-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="861fe-168">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="861fe-168">Element and component references are populated at this point.</span></span> <span data-ttu-id="861fe-169">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="861fe-169">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="861fe-170">El parámetro `firstRender` para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="861fe-170">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="861fe-171">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-171">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="861fe-172">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="861fe-172">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="861fe-173">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-173">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="861fe-174">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="861fe-174">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="861fe-175">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="861fe-175">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="861fe-176">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="861fe-176">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="861fe-177">*Durante el proceso de representación previa en el servidor no se llama a* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ni a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="861fe-178">Se llama a estos métodos cuando el componente se representa de forma interactiva una vez finalizada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="861fe-178">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="861fe-179">Cuando la aplicación se representa previamente:</span><span class="sxs-lookup"><span data-stu-id="861fe-179">When the app prerenders:</span></span>

1. <span data-ttu-id="861fe-180">El componente se ejecuta en el servidor para generar algo de marcado HTML estático en la respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="861fe-180">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="861fe-181">Durante esta fase, no se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ni a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="861fe-181">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="861fe-182">Cuando `blazor.server.js` o `blazor.webassembly.js` se inician en el explorador, el componente se reinicia en modo de representación interactiva.</span><span class="sxs-lookup"><span data-stu-id="861fe-182">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="861fe-183">Cuando un componente se reinicia, **sí se llama** a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> y a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, dado que la aplicación ya no está en fase de representación previa.</span><span class="sxs-lookup"><span data-stu-id="861fe-183">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="861fe-184">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="861fe-184">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="861fe-185">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="861fe-185">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="861fe-186">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="861fe-186">Suppress UI refreshing</span></span>

<span data-ttu-id="861fe-187">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="861fe-187">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="861fe-188">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="861fe-188">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="861fe-189">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="861fe-190">Aunque se invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="861fe-190">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="861fe-191">Para obtener más información, vea <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="861fe-191">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="861fe-192">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="861fe-192">State changes</span></span>

<span data-ttu-id="861fe-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="861fe-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="861fe-194">Cuando es aplicable, la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="861fe-194">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="861fe-195">A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se le llama automáticamente para métodos <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="861fe-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="861fe-196">Para obtener más información, vea <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="861fe-196">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="861fe-197">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="861fe-197">Handle incomplete async actions at render</span></span>

<span data-ttu-id="861fe-198">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-198">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="861fe-199">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="861fe-199">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="861fe-200">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="861fe-200">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="861fe-201">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="861fe-201">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="861fe-202">En el componente `FetchData` de las plantillas de Blazor, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="861fe-202">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="861fe-203">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="861fe-203">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="861fe-204">Una vez que se completa el elemento `Task` que devuelve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="861fe-204">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="861fe-205">`Pages/FetchData.razor` en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="861fe-205">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="861fe-206">Control de errores</span><span class="sxs-lookup"><span data-stu-id="861fe-206">Handle errors</span></span>

<span data-ttu-id="861fe-207">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="861fe-207">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="861fe-208">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="861fe-208">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="861fe-209">En una aplicación Blazor Server, cuando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> es <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="861fe-209">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="861fe-210">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="861fe-210">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="861fe-211">Si el método de ciclo de vida [`OnInitialized{Async}`](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="861fe-211">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="861fe-212">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="861fe-212">When the component is prerendered statically.</span></span>
* <span data-ttu-id="861fe-213">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="861fe-213">After the server connection has been established.</span></span>

<span data-ttu-id="861fe-214">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="861fe-214">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="861fe-215">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="861fe-215">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="861fe-216">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="861fe-216">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="861fe-217">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-217">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="861fe-218">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="861fe-218">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="861fe-219">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="861fe-219">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="861fe-220">Para obtener más información sobre <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, vea <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="861fe-220">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="861fe-221">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="861fe-221">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="861fe-222">Eliminación de componentes con IDisposable</span><span class="sxs-lookup"><span data-stu-id="861fe-222">Component disposal with IDisposable</span></span>

<span data-ttu-id="861fe-223">Si un componente implementa <xref:System.IDisposable>, se llama al [método `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="861fe-223">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="861fe-224">La eliminación puede realizarse en cualquier momento, incluso durante la [inicialización de componentes](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="861fe-224">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="861fe-225">El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="861fe-225">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="861fe-226">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="861fe-226">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="861fe-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="861fe-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="861fe-228">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="861fe-228">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="861fe-229">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="861fe-229">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="861fe-230">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="861fe-230">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="861fe-231">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="861fe-231">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="861fe-232">Trabajo en segundo plano cancelable</span><span class="sxs-lookup"><span data-stu-id="861fe-232">Cancelable background work</span></span>

<span data-ttu-id="861fe-233">Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos.</span><span class="sxs-lookup"><span data-stu-id="861fe-233">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="861fe-234">Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones.</span><span class="sxs-lookup"><span data-stu-id="861fe-234">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="861fe-235">Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.</span><span class="sxs-lookup"><span data-stu-id="861fe-235">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="861fe-236">Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="861fe-236">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="861fe-237">Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.</span><span class="sxs-lookup"><span data-stu-id="861fe-237">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="861fe-238">El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.</span><span class="sxs-lookup"><span data-stu-id="861fe-238">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="861fe-239">La prioridad de las tareas que se están ejecutando debe cambiarse.</span><span class="sxs-lookup"><span data-stu-id="861fe-239">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="861fe-240">La aplicación debe apagarse para volver a implementarla en el servidor.</span><span class="sxs-lookup"><span data-stu-id="861fe-240">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="861fe-241">Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="861fe-241">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="861fe-242">Para implementar un patrón de trabajo en segundo plano cancelable en un componente:</span><span class="sxs-lookup"><span data-stu-id="861fe-242">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="861fe-243">Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="861fe-243">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="861fe-244">Durante la [eliminación del componente](#component-disposal-with-idisposable), y en cualquier momento en el que interese realizar la cancelación mediante la cancelación manual del token, llame a [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para indicar que se debe cancelar el trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="861fe-244">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="861fe-245">Cuando se devuelva la llamada asincrónica, llame a <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> en el token.</span><span class="sxs-lookup"><span data-stu-id="861fe-245">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="861fe-246">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="861fe-246">In the following example:</span></span>

* <span data-ttu-id="861fe-247">`await Task.Delay(5000, cts.Token);` representa el trabajo asincrónico en segundo plano de ejecución prolongada.</span><span class="sxs-lookup"><span data-stu-id="861fe-247">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="861fe-248">`BackgroundResourceMethod` representa un método en segundo plano de ejecución prolongada que no debe iniciarse si se elimina `Resource` antes de llamar al método.</span><span class="sxs-lookup"><span data-stu-id="861fe-248">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
