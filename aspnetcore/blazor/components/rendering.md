---
title: Representación de componentes de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre la representación de componentes de Razor en aplicaciones Blazor de ASP.NET Core, incluido cuándo llamar a StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280039"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="8d19b-103">Representación de componentes de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d19b-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="8d19b-104">Los componentes se *deben* representar la primera vez que se agregan a la jerarquía de componentes por parte de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="8d19b-104">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="8d19b-105">Esta es la única vez que un componente se debe representar de forma estricta.</span><span class="sxs-lookup"><span data-stu-id="8d19b-105">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="8d19b-106">Los componentes *pueden* optar por representarse en cualquier otro momento según su propia lógica y convenciones.</span><span class="sxs-lookup"><span data-stu-id="8d19b-106">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="8d19b-107">Convenciones para `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="8d19b-107">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="8d19b-108">De forma predeterminada, los componentes de Razor (`.razor`) se heredan de la clase base <xref:Microsoft.AspNetCore.Components.ComponentBase>, que contiene la lógica para desencadenar la nueva representación en las siguientes ocasiones:</span><span class="sxs-lookup"><span data-stu-id="8d19b-108">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="8d19b-109">Después de aplicar un conjunto actualizado de parámetros desde un componente primario.</span><span class="sxs-lookup"><span data-stu-id="8d19b-109">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="8d19b-110">Después de aplicar un valor actualizado para un parámetro en cascada.</span><span class="sxs-lookup"><span data-stu-id="8d19b-110">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="8d19b-111">Después de la notificación de un evento y la invocación de uno de sus propios controladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="8d19b-111">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="8d19b-112">Después de una llamada a su propio método <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="8d19b-113">Los componentes heredados de <xref:Microsoft.AspNetCore.Components.ComponentBase> omiten las nuevas representaciones debido a las actualizaciones de parámetros si se cumple alguna de las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="8d19b-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="8d19b-114">Todos los valores de parámetro son de tipos primitivos inmutables conocidos (por ejemplo, `int`, `string` o `DateTime`) y no han cambiado desde que se ha establecido el conjunto anterior de parámetros.</span><span class="sxs-lookup"><span data-stu-id="8d19b-114">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="8d19b-115">El método <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> del componente devuelve `false`.</span><span class="sxs-lookup"><span data-stu-id="8d19b-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="8d19b-116">Para más información sobre <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consulte <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="8d19b-117">Control del flujo de representación</span><span class="sxs-lookup"><span data-stu-id="8d19b-117">Control the rendering flow</span></span>

<span data-ttu-id="8d19b-118">En la mayoría de los casos, las convenciones de <xref:Microsoft.AspNetCore.Components.ComponentBase> dan como resultado el subconjunto correcto de nuevas representaciones de componentes después de que se produzca un evento.</span><span class="sxs-lookup"><span data-stu-id="8d19b-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="8d19b-119">Normalmente, no es necesario que los desarrolladores proporcionen ninguna lógica manual para indicar al marco qué componentes se van a volver a representar y cuándo.</span><span class="sxs-lookup"><span data-stu-id="8d19b-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="8d19b-120">El efecto general de las convenciones del marco es que el componente que recibe un evento se vuelva a representar a sí mismo, lo que desencadena de forma recursiva la nueva representación de los componentes descendientes cuyos valores de parámetro puedan haber cambiado.</span><span class="sxs-lookup"><span data-stu-id="8d19b-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="8d19b-121">Para obtener más información sobre las implicaciones de rendimiento de las convenciones del marco y cómo optimizar la jerarquía de componentes de una aplicación, vea <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="8d19b-122">Cuándo llamar a `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="8d19b-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="8d19b-123">El método <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> permite desencadenar una representación en cualquier momento.</span><span class="sxs-lookup"><span data-stu-id="8d19b-123">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="8d19b-124">Pero debe evitar llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma innecesaria, lo que es un error común, ya que impone costos de representación innecesarios.</span><span class="sxs-lookup"><span data-stu-id="8d19b-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="8d19b-125">*No* es necesario llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> cuando:</span><span class="sxs-lookup"><span data-stu-id="8d19b-125">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="8d19b-126">Se controlan eventos de manera rutinaria, ya sea de forma sincrónica o asincrónica, porque <xref:Microsoft.AspNetCore.Components.ComponentBase> desencadena una representación para la mayoría de los controladores de eventos rutinarios.</span><span class="sxs-lookup"><span data-stu-id="8d19b-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="8d19b-127">Se implementa una lógica típica del ciclo de vida, como [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) o [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), ya sea de forma sincrónica o asincrónica, porque <xref:Microsoft.AspNetCore.Components.ComponentBase> desencadena una representación para los eventos de ciclo de vida típicos.</span><span class="sxs-lookup"><span data-stu-id="8d19b-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="8d19b-128">Pero es posible que tenga sentido llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en los casos descritos en las secciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="8d19b-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="8d19b-129">Un controlador asincrónico implica varias fases asincrónicas</span><span class="sxs-lookup"><span data-stu-id="8d19b-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="8d19b-130">Recepción de una llamada de algo externo al sistema de control de eventos y representación de Blazor</span><span class="sxs-lookup"><span data-stu-id="8d19b-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="8d19b-131">Para representar el componente fuera del subárbol que se vuelve a representar mediante un evento concreto</span><span class="sxs-lookup"><span data-stu-id="8d19b-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="8d19b-132">Un controlador asincrónico implica varias fases asincrónicas</span><span class="sxs-lookup"><span data-stu-id="8d19b-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="8d19b-133">Tenga en cuenta el siguiente componente de `Counter`, que actualiza el recuento cuatro veces en cada clic.</span><span class="sxs-lookup"><span data-stu-id="8d19b-133">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="8d19b-134">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8d19b-134">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="8d19b-135">Debido al modo en el que se definen las tareas en .NET, un receptor de una instancia de <xref:System.Threading.Tasks.Task> solo puede observar su finalización final, no los estados asincrónicos intermedios.</span><span class="sxs-lookup"><span data-stu-id="8d19b-135">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="8d19b-136">Por tanto, <xref:Microsoft.AspNetCore.Components.ComponentBase> solo puede desencadenar la nueva representación cuando se devuelve <xref:System.Threading.Tasks.Task> por primera vez y cuando se completa <xref:System.Threading.Tasks.Task> finalmente.</span><span class="sxs-lookup"><span data-stu-id="8d19b-136">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="8d19b-137">No puede saber que se debe volver a representar en otros puntos intermedios.</span><span class="sxs-lookup"><span data-stu-id="8d19b-137">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="8d19b-138">Si quiere repetir la representación en puntos intermedios, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-138">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="8d19b-139">Recepción de una llamada de algo externo al sistema de control de eventos y representación de Blazor</span><span class="sxs-lookup"><span data-stu-id="8d19b-139">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="8d19b-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> solo conoce sus propios métodos de ciclo de vida y los eventos desencadenados por Blazor.</span><span class="sxs-lookup"><span data-stu-id="8d19b-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="8d19b-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> no conoce otros eventos que se pueden producir en el código.</span><span class="sxs-lookup"><span data-stu-id="8d19b-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="8d19b-142">Por ejemplo, los eventos de C# generados por un almacén de datos personalizado son desconocidos para Blazor.</span><span class="sxs-lookup"><span data-stu-id="8d19b-142">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="8d19b-143">Para que estos eventos desencadenen la nueva representación a fin de mostrar valores actualizados en la interfaz de usuario, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-143">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="8d19b-144">En otro caso de uso, considere el siguiente componente de `Counter`, el cual usa <xref:System.Timers.Timer?displayProperty=fullName> para actualizar el recuento a intervalos regulares y llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para actualizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="8d19b-144">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="8d19b-145">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="8d19b-145">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="8d19b-146">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="8d19b-146">In the preceding example:</span></span>

* <span data-ttu-id="8d19b-147">`OnTimerCallback` debe llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> porque Blazor no es consciente de los cambios en `currentCount` en la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="8d19b-147">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="8d19b-148">`OnTimerCallback` se ejecuta fuera de cualquier flujo de representación o notificación de eventos administrado por Blazor.</span><span class="sxs-lookup"><span data-stu-id="8d19b-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="8d19b-149">El componente implementa <xref:System.IDisposable>, donde <xref:System.Timers.Timer> se desecha cuando el marco llama al método `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="8d19b-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="8d19b-150">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="8d19b-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="8d19b-151">Del mismo modo, como la devolución de llamada se invoca fuera del contexto de sincronización de Blazor, es necesario encapsular la lógica en <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> para moverla al contexto de sincronización del representador.</span><span class="sxs-lookup"><span data-stu-id="8d19b-151">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="8d19b-152">Solo se puede llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> desde el contexto de sincronización del representador; en caso contrario se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="8d19b-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="8d19b-153">Esto equivale a la serialización en el subproceso de interfaz de usuario en otros marcos de interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="8d19b-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="8d19b-154">Para representar el componente fuera del subárbol que se vuelve a representar mediante un evento concreto</span><span class="sxs-lookup"><span data-stu-id="8d19b-154">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="8d19b-155">Es posible que la interfaz de usuario implique el envío de un evento a un componente, la modificación de algún estado y la necesidad de volver a representar un componente totalmente distinto que no sea un descendiente del que recibe el evento.</span><span class="sxs-lookup"><span data-stu-id="8d19b-155">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="8d19b-156">Una manera de abordar este escenario consiste en que una clase de *administración de estado*, como un servicio de DI, se inserte en varios componentes.</span><span class="sxs-lookup"><span data-stu-id="8d19b-156">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="8d19b-157">Cuando un componente llama a un método en el administrador de estado, este puede generar un evento de C# que, después, lo recibe un componente independiente.</span><span class="sxs-lookup"><span data-stu-id="8d19b-157">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="8d19b-158">Como estos eventos de C# están fuera de la canalización de representación de Blazor, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en otros componentes que quiera representar en respuesta a los eventos del administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="8d19b-158">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="8d19b-159">Esto es similar al caso previo con <xref:System.Timers.Timer?displayProperty=fullName> en la [sección anterior](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system).</span><span class="sxs-lookup"><span data-stu-id="8d19b-159">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="8d19b-160">Como la pila de llamadas de ejecución normalmente permanece en el contexto de sincronización del representador, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> no suele ser necesario.</span><span class="sxs-lookup"><span data-stu-id="8d19b-160">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="8d19b-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> solo es necesario si la lógica escapa al contexto de sincronización, como al llamar a <xref:System.Threading.Tasks.Task.ContinueWith%2A> en <xref:System.Threading.Tasks.Task> o esperar por <xref:System.Threading.Tasks.Task> con [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span><span class="sxs-lookup"><span data-stu-id="8d19b-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
