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
# <a name="aspnet-core-blazor-component-rendering"></a>Representación de componentes de Blazor de ASP.NET Core

Los componentes se *deben* representar la primera vez que se agregan a la jerarquía de componentes por parte de su componente primario. Esta es la única vez que un componente se debe representar de forma estricta.

Los componentes *pueden* optar por representarse en cualquier otro momento según su propia lógica y convenciones.

## <a name="conventions-for-componentbase"></a>Convenciones para `ComponentBase`

De forma predeterminada, los componentes de Razor (`.razor`) se heredan de la clase base <xref:Microsoft.AspNetCore.Components.ComponentBase>, que contiene la lógica para desencadenar la nueva representación en las siguientes ocasiones:

* Después de aplicar un conjunto actualizado de parámetros desde un componente primario.
* Después de aplicar un valor actualizado para un parámetro en cascada.
* Después de la notificación de un evento y la invocación de uno de sus propios controladores de eventos.
* Después de una llamada a su propio método <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

Los componentes heredados de <xref:Microsoft.AspNetCore.Components.ComponentBase> omiten las nuevas representaciones debido a las actualizaciones de parámetros si se cumple alguna de las condiciones siguientes:

* Todos los valores de parámetro son de tipos primitivos inmutables conocidos (por ejemplo, `int`, `string` o `DateTime`) y no han cambiado desde que se ha establecido el conjunto anterior de parámetros.
* El método <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> del componente devuelve `false`.

Para más información sobre <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consulte <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.

## <a name="control-the-rendering-flow"></a>Control del flujo de representación

En la mayoría de los casos, las convenciones de <xref:Microsoft.AspNetCore.Components.ComponentBase> dan como resultado el subconjunto correcto de nuevas representaciones de componentes después de que se produzca un evento. Normalmente, no es necesario que los desarrolladores proporcionen ninguna lógica manual para indicar al marco qué componentes se van a volver a representar y cuándo. El efecto general de las convenciones del marco es que el componente que recibe un evento se vuelva a representar a sí mismo, lo que desencadena de forma recursiva la nueva representación de los componentes descendientes cuyos valores de parámetro puedan haber cambiado.

Para obtener más información sobre las implicaciones de rendimiento de las convenciones del marco y cómo optimizar la jerarquía de componentes de una aplicación, vea <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.

## <a name="when-to-call-statehaschanged"></a>Cuándo llamar a `StateHasChanged`

El método <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> permite desencadenar una representación en cualquier momento. Pero debe evitar llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma innecesaria, lo que es un error común, ya que impone costos de representación innecesarios.

*No* es necesario llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> cuando:

* Se controlan eventos de manera rutinaria, ya sea de forma sincrónica o asincrónica, porque <xref:Microsoft.AspNetCore.Components.ComponentBase> desencadena una representación para la mayoría de los controladores de eventos rutinarios.
* Se implementa una lógica típica del ciclo de vida, como [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) o [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), ya sea de forma sincrónica o asincrónica, porque <xref:Microsoft.AspNetCore.Components.ComponentBase> desencadena una representación para los eventos de ciclo de vida típicos.

Pero es posible que tenga sentido llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en los casos descritos en las secciones siguientes:

* [Un controlador asincrónico implica varias fases asincrónicas](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Recepción de una llamada de algo externo al sistema de control de eventos y representación de Blazor](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Para representar el componente fuera del subárbol que se vuelve a representar mediante un evento concreto](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Un controlador asincrónico implica varias fases asincrónicas

Tenga en cuenta el siguiente componente de `Counter`, que actualiza el recuento cuatro veces en cada clic.

`Pages/Counter.razor`:

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

Debido al modo en el que se definen las tareas en .NET, un receptor de una instancia de <xref:System.Threading.Tasks.Task> solo puede observar su finalización final, no los estados asincrónicos intermedios. Por tanto, <xref:Microsoft.AspNetCore.Components.ComponentBase> solo puede desencadenar la nueva representación cuando se devuelve <xref:System.Threading.Tasks.Task> por primera vez y cuando se completa <xref:System.Threading.Tasks.Task> finalmente. No puede saber que se debe volver a representar en otros puntos intermedios. Si quiere repetir la representación en puntos intermedios, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>Recepción de una llamada de algo externo al sistema de control de eventos y representación de Blazor

<xref:Microsoft.AspNetCore.Components.ComponentBase> solo conoce sus propios métodos de ciclo de vida y los eventos desencadenados por Blazor. <xref:Microsoft.AspNetCore.Components.ComponentBase> no conoce otros eventos que se pueden producir en el código. Por ejemplo, los eventos de C# generados por un almacén de datos personalizado son desconocidos para Blazor. Para que estos eventos desencadenen la nueva representación a fin de mostrar valores actualizados en la interfaz de usuario, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

En otro caso de uso, considere el siguiente componente de `Counter`, el cual usa <xref:System.Timers.Timer?displayProperty=fullName> para actualizar el recuento a intervalos regulares y llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para actualizar la interfaz de usuario.

`Pages/CounterWithTimerDisposal.razor`:

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

En el ejemplo anterior:

* `OnTimerCallback` debe llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> porque Blazor no es consciente de los cambios en `currentCount` en la devolución de llamada. `OnTimerCallback` se ejecuta fuera de cualquier flujo de representación o notificación de eventos administrado por Blazor.
* El componente implementa <xref:System.IDisposable>, donde <xref:System.Timers.Timer> se desecha cuando el marco llama al método `Dispose`. Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

Del mismo modo, como la devolución de llamada se invoca fuera del contexto de sincronización de Blazor, es necesario encapsular la lógica en <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> para moverla al contexto de sincronización del representador. Solo se puede llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> desde el contexto de sincronización del representador; en caso contrario se inicia una excepción. Esto equivale a la serialización en el subproceso de interfaz de usuario en otros marcos de interfaz de usuario.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Para representar el componente fuera del subárbol que se vuelve a representar mediante un evento concreto

Es posible que la interfaz de usuario implique el envío de un evento a un componente, la modificación de algún estado y la necesidad de volver a representar un componente totalmente distinto que no sea un descendiente del que recibe el evento.

Una manera de abordar este escenario consiste en que una clase de *administración de estado*, como un servicio de DI, se inserte en varios componentes. Cuando un componente llama a un método en el administrador de estado, este puede generar un evento de C# que, después, lo recibe un componente independiente.

Como estos eventos de C# están fuera de la canalización de representación de Blazor, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en otros componentes que quiera representar en respuesta a los eventos del administrador de estado.

Esto es similar al caso previo con <xref:System.Timers.Timer?displayProperty=fullName> en la [sección anterior](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system). Como la pila de llamadas de ejecución normalmente permanece en el contexto de sincronización del representador, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> no suele ser necesario. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> solo es necesario si la lógica escapa al contexto de sincronización, como al llamar a <xref:System.Threading.Tasks.Task.ContinueWith%2A> en <xref:System.Threading.Tasks.Task> o esperar por <xref:System.Threading.Tasks.Task> con [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).
