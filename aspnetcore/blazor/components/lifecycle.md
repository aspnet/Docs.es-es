---
title: Ciclo de vida de ASP.NET Core Blazor
author: guardrex
description: Aprenda a usar los métodos de ciclo de vida de los componentes de Razor en aplicaciones de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: d1a17f26ac89a2d1d722e37faf3a90f735b23f94
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628448"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="eb1d5-103">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="eb1d5-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="eb1d5-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="eb1d5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="eb1d5-105">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="eb1d5-106">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="eb1d5-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="eb1d5-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="eb1d5-108">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="eb1d5-108">Before parameters are set</span></span>

<span data-ttu-id="eb1d5-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="eb1d5-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene todo el conjunto de valores de parámetro cada vez que se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="eb1d5-111">La implementación predeterminada de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece el valor de cada propiedad con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), que tiene un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="eb1d5-112">Los parámetros que no tienen un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView> se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="eb1d5-113">Si no se invoca [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A), el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="eb1d5-114">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="eb1d5-115">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="eb1d5-116">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="eb1d5-117">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="eb1d5-117">Component initialization methods</span></span>

<span data-ttu-id="eb1d5-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario en <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="eb1d5-119">Utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="eb1d5-120">En el caso de una operación sincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="eb1d5-121">Para realizar una operación asincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y use el operador [`await`](/dotnet/csharp/language-reference/operators/await) en la operación:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="eb1d5-122">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/fundamentals/additional-scenarios#render-mode) llaman a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dos veces_**:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-122">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="eb1d5-123">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="eb1d5-124">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="eb1d5-125">Para evitar que el código de desarrollador en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="eb1d5-126">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="eb1d5-127">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="eb1d5-128">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="eb1d5-129">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="eb1d5-130">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="eb1d5-131">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="eb1d5-131">After parameters are set</span></span>

<span data-ttu-id="eb1d5-132">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="eb1d5-133">Después de inicializar el componente en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="eb1d5-134">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="eb1d5-135">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="eb1d5-136">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-136">Any complex-typed parameters.</span></span> <span data-ttu-id="eb1d5-137">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="eb1d5-138">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="eb1d5-139">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="eb1d5-140">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="eb1d5-141">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="eb1d5-141">After component render</span></span>

<span data-ttu-id="eb1d5-142">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="eb1d5-143">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="eb1d5-144">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="eb1d5-145">El parámetro `firstRender` para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="eb1d5-146">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="eb1d5-147">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="eb1d5-148">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="eb1d5-149">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="eb1d5-150">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="eb1d5-151">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="eb1d5-152">*No se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> al representarse previamente en el servidor.*</span><span class="sxs-lookup"><span data-stu-id="eb1d5-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="eb1d5-153">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="eb1d5-154">Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="eb1d5-155">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="eb1d5-155">Suppress UI refreshing</span></span>

<span data-ttu-id="eb1d5-156">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="eb1d5-157">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="eb1d5-158">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="eb1d5-159">Aunque se invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="eb1d5-160">Para obtener más información, vea <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="eb1d5-161">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="eb1d5-161">State changes</span></span>

<span data-ttu-id="eb1d5-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="eb1d5-163">Cuando es aplicable, la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="eb1d5-164">A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se le llama automáticamente para métodos <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="eb1d5-165">Para obtener más información, vea <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-165">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="eb1d5-166">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="eb1d5-166">Handle incomplete async actions at render</span></span>

<span data-ttu-id="eb1d5-167">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-167">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="eb1d5-168">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-168">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="eb1d5-169">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-169">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="eb1d5-170">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-170">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="eb1d5-171">En el componente `FetchData` de las plantillas de Blazor, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-171">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="eb1d5-172">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-172">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="eb1d5-173">Una vez que se completa el elemento `Task` que devuelve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-173">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="eb1d5-174">`Pages/FetchData.razor` en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-174">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="eb1d5-175">Control de errores</span><span class="sxs-lookup"><span data-stu-id="eb1d5-175">Handle errors</span></span>

<span data-ttu-id="eb1d5-176">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-176">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="eb1d5-177">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="eb1d5-177">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="eb1d5-178">En una aplicación Blazor Server, cuando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> es <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-178">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="eb1d5-179">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-179">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="eb1d5-180">Si el método de ciclo de vida [`OnInitialized{Async}`](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-180">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="eb1d5-181">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-181">When the component is prerendered statically.</span></span>
* <span data-ttu-id="eb1d5-182">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-182">After the server connection has been established.</span></span>

<span data-ttu-id="eb1d5-183">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-183">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="eb1d5-184">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-184">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="eb1d5-185">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-185">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="eb1d5-186">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-186">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="eb1d5-187">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-187">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="eb1d5-188">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-188">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="eb1d5-189">Para obtener más información sobre <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, vea <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-189">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="eb1d5-190">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="eb1d5-190">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="eb1d5-191">Eliminación de componentes con IDisposable</span><span class="sxs-lookup"><span data-stu-id="eb1d5-191">Component disposal with IDisposable</span></span>

<span data-ttu-id="eb1d5-192">Si un componente implementa <xref:System.IDisposable>, se llama al [método `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-192">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="eb1d5-193">La eliminación puede realizarse en cualquier momento, incluso durante la [inicialización de componentes](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="eb1d5-193">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="eb1d5-194">El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-194">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="eb1d5-195">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-195">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="eb1d5-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="eb1d5-197">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-197">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="eb1d5-198">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-198">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="eb1d5-199">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="eb1d5-199">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="eb1d5-200">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="eb1d5-200">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="eb1d5-201">Trabajo en segundo plano cancelable</span><span class="sxs-lookup"><span data-stu-id="eb1d5-201">Cancelable background work</span></span>

<span data-ttu-id="eb1d5-202">Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-202">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="eb1d5-203">Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-203">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="eb1d5-204">Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-204">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="eb1d5-205">Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-205">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="eb1d5-206">Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-206">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="eb1d5-207">El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-207">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="eb1d5-208">La prioridad de las tareas que se están ejecutando debe cambiarse.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-208">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="eb1d5-209">La aplicación debe apagarse para volver a implementarla en el servidor.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-209">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="eb1d5-210">Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-210">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="eb1d5-211">Para implementar un patrón de trabajo en segundo plano cancelable en un componente:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-211">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="eb1d5-212">Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-212">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="eb1d5-213">Durante la [eliminación del componente](#component-disposal-with-idisposable), y en cualquier momento en el que interese realizar la cancelación mediante la cancelación manual del token, llame a [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para indicar que se debe cancelar el trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-213">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="eb1d5-214">Cuando se devuelva la llamada asincrónica, llame a <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> en el token.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-214">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="eb1d5-215">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb1d5-215">In the following example:</span></span>

* <span data-ttu-id="eb1d5-216">`await Task.Delay(5000, cts.Token);` representa el trabajo asincrónico en segundo plano de ejecución prolongada.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-216">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="eb1d5-217">`BackgroundResourceMethod` representa un método en segundo plano de ejecución prolongada que no debe iniciarse si se elimina `Resource` antes de llamar al método.</span><span class="sxs-lookup"><span data-stu-id="eb1d5-217">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
