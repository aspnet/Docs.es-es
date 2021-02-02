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
ms.openlocfilehash: 3591ba18351b89e2d5dfaef796777273c97ce98b
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751615"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="da6f8-103">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="da6f8-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="da6f8-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="da6f8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="da6f8-105">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="da6f8-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="da6f8-106">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="da6f8-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="da6f8-107">Los siguientes diagramas ilustran el ciclo de vida de Blazor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="da6f8-108">Los métodos de ciclo de vida se definen con ejemplos en las siguientes secciones de este artículo.</span><span class="sxs-lookup"><span data-stu-id="da6f8-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="da6f8-109">Eventos del ciclo de vida del componente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="da6f8-110">Si el componente se representa por primera vez en una solicitud:</span><span class="sxs-lookup"><span data-stu-id="da6f8-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="da6f8-111">Cree la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-111">Create the component's instance.</span></span>
   * <span data-ttu-id="da6f8-112">Realice la inserción de propiedades.</span><span class="sxs-lookup"><span data-stu-id="da6f8-112">Perform property injection.</span></span> <span data-ttu-id="da6f8-113">Ejecute [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="da6f8-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="da6f8-114">Llame a [`OnInitialized{Async}`](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="da6f8-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="da6f8-115">Si se devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="da6f8-116">Si no se devuelve <xref:System.Threading.Tasks.Task>, se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="da6f8-117">Llame a [`OnParametersSet{Async}`](#after-parameters-are-set) y represente el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="da6f8-118">Si `OnParametersSetAsync` devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y, a continuación, se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Eventos de ciclo de vida de componentes de un componente Razor en Blazor](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="da6f8-120">Procesamiento de eventos de Document Object Model (DOM):</span><span class="sxs-lookup"><span data-stu-id="da6f8-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="da6f8-121">Se ejecuta el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="da6f8-121">The event handler is run.</span></span>
1. <span data-ttu-id="da6f8-122">Si se devuelve <xref:System.Threading.Tasks.Task>, se esperará <xref:System.Threading.Tasks.Task> y, a continuación, se representará el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="da6f8-123">Si no se devuelve <xref:System.Threading.Tasks.Task>, se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Procesamiento de eventos de Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="da6f8-125">Ciclo de vida de `Render`:</span><span class="sxs-lookup"><span data-stu-id="da6f8-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="da6f8-126">Evite operaciones de representación adicionales en el componente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="da6f8-127">Después de la primera representación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-127">After the first render.</span></span>
   * <span data-ttu-id="da6f8-128">Cuando [`ShouldRender`](#suppress-ui-refreshing) es `false`.</span><span class="sxs-lookup"><span data-stu-id="da6f8-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="da6f8-129">Compile la diff (comparación) del árbol de representación y represente el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="da6f8-130">Espere a que se actualice DOM.</span><span class="sxs-lookup"><span data-stu-id="da6f8-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="da6f8-131">Llame a [`OnAfterRender{Async}`](#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="da6f8-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Ciclo de vida de representación](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="da6f8-133">Las llamadas del desarrollador a [`StateHasChanged`](#state-changes) producen una representación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="da6f8-134">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="da6f8-135">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="da6f8-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="da6f8-136">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="da6f8-136">Before parameters are set</span></span>

<span data-ttu-id="da6f8-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación o a partir de los parámetros de ruta.</span><span class="sxs-lookup"><span data-stu-id="da6f8-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="da6f8-138">Al invalidar el método, el código de desarrollador puede interactuar directamente con los parámetros de <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-138">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="da6f8-139">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> asigna el valor del parámetro `Param` a `value` si el análisis de un parámetro de ruta para `Param` se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-139">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="da6f8-140">Cuando `value` no es `null`, el componente `SetParametersAsyncExample` muestra el valor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-140">When `value` isn't `null`, the value is displayed by the `SetParametersAsyncExample` component.</span></span>

<span data-ttu-id="da6f8-141">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="da6f8-141">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            message = $"The value of 'Param' is {value}.";
        }
        else 
        {
            message = "The value of 'Param' is null.";
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="da6f8-142"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene el conjunto de valores de parámetros del componente cada vez que se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-142"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="da6f8-143">La implementación predeterminada de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece el valor de cada propiedad con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), que tiene un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-143">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="da6f8-144">Los parámetros que no tienen un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView> se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="da6f8-144">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="da6f8-145">Si no se invoca [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A), el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="da6f8-145">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="da6f8-146">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="da6f8-146">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="da6f8-147">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-147">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="da6f8-148">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="da6f8-148">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="da6f8-149">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="da6f8-149">Component initialization methods</span></span>

<span data-ttu-id="da6f8-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario en <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="da6f8-151">Utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-151">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="da6f8-152">En el caso de una operación sincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="da6f8-152">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="da6f8-153">Para realizar una operación asincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y use el operador [`await`](/dotnet/csharp/language-reference/operators/await) en la operación:</span><span class="sxs-lookup"><span data-stu-id="da6f8-153">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="da6f8-154">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/fundamentals/additional-scenarios#render-mode) llaman a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="da6f8-154">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="da6f8-155">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="da6f8-155">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="da6f8-156">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-156">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="da6f8-157">Para evitar que el código de desarrollador en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="da6f8-157">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="da6f8-158">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="da6f8-158">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="da6f8-159">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-159">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="da6f8-160">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="da6f8-160">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="da6f8-161">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-161">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="da6f8-162">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="da6f8-162">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="da6f8-163">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="da6f8-163">After parameters are set</span></span>

<span data-ttu-id="da6f8-164">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:</span><span class="sxs-lookup"><span data-stu-id="da6f8-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="da6f8-165">Después de inicializar el componente en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-165">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="da6f8-166">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-166">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="da6f8-167">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="da6f8-167">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="da6f8-168">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="da6f8-168">Any complex-typed parameters.</span></span> <span data-ttu-id="da6f8-169">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="da6f8-169">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="da6f8-170">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-170">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="da6f8-171">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-171">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="da6f8-172">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="da6f8-172">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="da6f8-173">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="da6f8-173">After component render</span></span>

<span data-ttu-id="da6f8-174">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-174"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="da6f8-175">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="da6f8-175">Element and component references are populated at this point.</span></span> <span data-ttu-id="da6f8-176">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="da6f8-176">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="da6f8-177">El parámetro `firstRender` para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="da6f8-177">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="da6f8-178">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-178">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="da6f8-179">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="da6f8-179">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="da6f8-180">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-180">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="da6f8-181">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="da6f8-181">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="da6f8-182">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="da6f8-182">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="da6f8-183">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="da6f8-183">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="da6f8-184">*Durante el proceso de representación previa en el servidor no se llama a* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ni a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-184"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="da6f8-185">Se llama a estos métodos cuando el componente se representa de forma interactiva una vez finalizada la representación previa.</span><span class="sxs-lookup"><span data-stu-id="da6f8-185">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="da6f8-186">Cuando la aplicación se representa previamente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-186">When the app prerenders:</span></span>

1. <span data-ttu-id="da6f8-187">El componente se ejecuta en el servidor para generar algo de marcado HTML estático en la respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="da6f8-187">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="da6f8-188">Durante esta fase, no se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ni a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-188">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="da6f8-189">Cuando `blazor.server.js` o `blazor.webassembly.js` se inician en el explorador, el componente se reinicia en modo de representación interactiva.</span><span class="sxs-lookup"><span data-stu-id="da6f8-189">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="da6f8-190">Cuando un componente se reinicia, **sí se llama** a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> y a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, dado que la aplicación ya no está en fase de representación previa.</span><span class="sxs-lookup"><span data-stu-id="da6f8-190">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="da6f8-191">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-191">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="da6f8-192">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="da6f8-192">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="da6f8-193">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="da6f8-193">Suppress UI refreshing</span></span>

<span data-ttu-id="da6f8-194">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="da6f8-194">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="da6f8-195">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="da6f8-195">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="da6f8-196">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="da6f8-197">Aunque se invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-197">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="da6f8-198">Para obtener más información, vea <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-198">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="da6f8-199">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="da6f8-199">State changes</span></span>

<span data-ttu-id="da6f8-200"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="da6f8-200"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="da6f8-201">Cuando es aplicable, la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-201">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="da6f8-202">A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se le llama automáticamente para métodos <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="da6f8-203">Para obtener más información, vea <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-203">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="da6f8-204">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-204">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="da6f8-205">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="da6f8-205">Handle incomplete async actions at render</span></span>

<span data-ttu-id="da6f8-206">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-206">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="da6f8-207">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="da6f8-207">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="da6f8-208">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="da6f8-208">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="da6f8-209">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="da6f8-209">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="da6f8-210">En el componente `FetchData` de las plantillas de Blazor, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="da6f8-210">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="da6f8-211">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="da6f8-211">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="da6f8-212">Una vez que se completa el elemento `Task` que devuelve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="da6f8-212">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="da6f8-213">`Pages/FetchData.razor` en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="da6f8-213">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="da6f8-214">Control de errores</span><span class="sxs-lookup"><span data-stu-id="da6f8-214">Handle errors</span></span>

<span data-ttu-id="da6f8-215">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-215">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="da6f8-216">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="da6f8-216">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="da6f8-217">En una aplicación Blazor Server, cuando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> es <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="da6f8-217">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="da6f8-218">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="da6f8-218">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="da6f8-219">Si el método de ciclo de vida [`OnInitialized{Async}`](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="da6f8-219">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="da6f8-220">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="da6f8-220">When the component is prerendered statically.</span></span>
* <span data-ttu-id="da6f8-221">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-221">After the server connection has been established.</span></span>

<span data-ttu-id="da6f8-222">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-222">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="da6f8-223">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="da6f8-223">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="da6f8-224">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="da6f8-224">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="da6f8-225">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-225">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="da6f8-226">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="da6f8-226">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="da6f8-227">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="da6f8-227">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="da6f8-228">Para obtener más información sobre <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, vea <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-228">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="da6f8-229">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="da6f8-229">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="da6f8-230">Eliminación de componentes con `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="da6f8-230">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="da6f8-231">Si un componente implementa <xref:System.IDisposable>, el marco llama al [método de eliminación](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario, donde se pueden liberar los recursos no administrados.</span><span class="sxs-lookup"><span data-stu-id="da6f8-231">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="da6f8-232">La eliminación puede realizarse en cualquier momento, incluso durante la [inicialización de componentes](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="da6f8-232">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="da6f8-233">El componente siguiente implementa <xref:System.IDisposable> con la directiva [`@implements`](xref:mvc/views/razor#implements) Razor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-233">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="da6f8-234">En el caso de las tareas de eliminación asincrónica, use `DisposeAsync` en lugar de `Dispose` en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="da6f8-234">For asynchronous disposal tasks, use `DisposeAsync` instead of `Dispose` in the preceding example:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="da6f8-235">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="da6f8-235">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="da6f8-236"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="da6f8-236"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="da6f8-237">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="da6f8-237">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="da6f8-238">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="da6f8-238">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="da6f8-239">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="da6f8-239">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="da6f8-240">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="da6f8-240">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]
  
<span data-ttu-id="da6f8-241">Para obtener más información, vea [Limpiar recursos no administrados](/dotnet/standard/garbage-collection/unmanaged) y los temas siguientes sobre la implementación de los métodos `Dispose` y `DisposeAsync`.</span><span class="sxs-lookup"><span data-stu-id="da6f8-241">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="da6f8-242">Trabajo en segundo plano cancelable</span><span class="sxs-lookup"><span data-stu-id="da6f8-242">Cancelable background work</span></span>

<span data-ttu-id="da6f8-243">Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos.</span><span class="sxs-lookup"><span data-stu-id="da6f8-243">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="da6f8-244">Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones.</span><span class="sxs-lookup"><span data-stu-id="da6f8-244">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="da6f8-245">Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.</span><span class="sxs-lookup"><span data-stu-id="da6f8-245">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="da6f8-246">Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="da6f8-246">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="da6f8-247">Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.</span><span class="sxs-lookup"><span data-stu-id="da6f8-247">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="da6f8-248">El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.</span><span class="sxs-lookup"><span data-stu-id="da6f8-248">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="da6f8-249">La prioridad de las tareas que se están ejecutando debe cambiarse.</span><span class="sxs-lookup"><span data-stu-id="da6f8-249">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="da6f8-250">La aplicación debe apagarse para volver a implementarla en el servidor.</span><span class="sxs-lookup"><span data-stu-id="da6f8-250">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="da6f8-251">Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="da6f8-251">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="da6f8-252">Para implementar un patrón de trabajo en segundo plano cancelable en un componente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-252">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="da6f8-253">Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-253">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="da6f8-254">Durante la [eliminación del componente](#component-disposal-with-idisposable), y en cualquier momento en el que interese realizar la cancelación mediante la cancelación manual del token, llame a [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para indicar que se debe cancelar el trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="da6f8-254">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="da6f8-255">Cuando se devuelva la llamada asincrónica, llame a <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> en el token.</span><span class="sxs-lookup"><span data-stu-id="da6f8-255">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="da6f8-256">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="da6f8-256">In the following example:</span></span>

* <span data-ttu-id="da6f8-257">`await Task.Delay(5000, cts.Token);` representa el trabajo asincrónico en segundo plano de ejecución prolongada.</span><span class="sxs-lookup"><span data-stu-id="da6f8-257">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="da6f8-258">`BackgroundResourceMethod` representa un método en segundo plano de ejecución prolongada que no debe iniciarse si se elimina `Resource` antes de llamar al método.</span><span class="sxs-lookup"><span data-stu-id="da6f8-258">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="da6f8-259">Eventos de reconexión de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="da6f8-259">Blazor Server reconnection events</span></span>

<span data-ttu-id="da6f8-260">Los eventos de ciclo de vida de componentes que se tratan en este artículo funcionan independientemente de los [controladores de eventos de reconexión de Blazor Server](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="da6f8-260">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="da6f8-261">Cuando una aplicación Blazor Server pierde su conexión SignalR con el cliente, solo se interrumpen las actualizaciones de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="da6f8-261">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="da6f8-262">Dichas actualizaciones se reanudan cuando se restablece la conexión.</span><span class="sxs-lookup"><span data-stu-id="da6f8-262">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="da6f8-263">Para obtener más información sobre los eventos de controlador de circuito y su configuración, vea <xref:blazor/fundamentals/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="da6f8-263">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
