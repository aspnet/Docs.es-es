---
title: Escenarios avanzados de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre escenarios avanzados en Blazor, incluido cómo incorporar la lógica RenderTreeBuilder manual en una aplicación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051789"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="0e79a-103">Escenarios avanzados de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0e79a-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="0e79a-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0e79a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="0e79a-105">Controlador de circuito de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="0e79a-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="0e79a-106">Blazor Server permite que el código defina un *controlador de circuito*, que permite ejecutar el código en los cambios realizados en el estado del circuito de un usuario.</span><span class="sxs-lookup"><span data-stu-id="0e79a-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="0e79a-107">Un controlador de circuito se implementa derivando de `CircuitHandler` y registrando la clase en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0e79a-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="0e79a-108">El ejemplo siguiente de un controlador de circuito realiza un seguimiento de las conexiones abiertas de SignalR:</span><span class="sxs-lookup"><span data-stu-id="0e79a-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="0e79a-109">Los controladores de circuito se registran mediante DI.</span><span class="sxs-lookup"><span data-stu-id="0e79a-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="0e79a-110">Las instancias con ámbito se crean por instancia de un circuito.</span><span class="sxs-lookup"><span data-stu-id="0e79a-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="0e79a-111">Mediante el uso de `TrackingCircuitHandler` en el ejemplo anterior, se crea un servicio singleton porque se debe realizar un seguimiento del estado de todos los circuitos:</span><span class="sxs-lookup"><span data-stu-id="0e79a-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="0e79a-112">Si los métodos de un controlador de circuito personalizado producen una excepción no controlada, la excepción es grave para el circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0e79a-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="0e79a-113">Para tolerar excepciones en el código de un controlador o en los métodos llamados, encapsule el código en una o varias instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="0e79a-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="0e79a-114">Cuando un circuito finaliza porque un usuario se ha desconectado y el marco está limpiando el estado del circuito, el marco desecha el ámbito de DI del circuito.</span><span class="sxs-lookup"><span data-stu-id="0e79a-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="0e79a-115">Al desechar el ámbito, se eliminan también todos los servicios de DI con ámbito del circuito que implementan <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0e79a-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="0e79a-116">Si un servicio de DI produce una excepción no controlada durante la eliminación, el marco registra la excepción.</span><span class="sxs-lookup"><span data-stu-id="0e79a-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="0e79a-117">Lógica Manual RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="0e79a-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="0e79a-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> proporciona métodos para manipular componentes y elementos, incluida la compilación manual de componentes en código de C#.</span><span class="sxs-lookup"><span data-stu-id="0e79a-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="0e79a-119">El uso de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para crear componentes es un escenario avanzado.</span><span class="sxs-lookup"><span data-stu-id="0e79a-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="0e79a-120">Un componente con formato incorrecto (por ejemplo, una etiqueta de marcado sin cerrar) puede dar como resultado un comportamiento indefinido.</span><span class="sxs-lookup"><span data-stu-id="0e79a-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="0e79a-121">Tenga en cuenta el componente `PetDetails` siguiente, que se puede integrar manualmente en otro componente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="0e79a-122">En el ejemplo siguiente, el bucle en el método `CreateComponent` genera tres componentes `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="0e79a-123">En métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> con un número de secuencia, los números de secuencia son números de línea de código fuente.</span><span class="sxs-lookup"><span data-stu-id="0e79a-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="0e79a-124">El algoritmo de diferencia Blazor se basa en los números de secuencia correspondientes a líneas de código distintas, no a invocaciones de llamada distintas.</span><span class="sxs-lookup"><span data-stu-id="0e79a-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="0e79a-125">Al crear un componente con métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, codifique los argumentos para los números de secuencia.</span><span class="sxs-lookup"><span data-stu-id="0e79a-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="0e79a-126">**El uso de un cálculo o un contador para generar el número de secuencia puede dar lugar a un rendimiento deficiente.**</span><span class="sxs-lookup"><span data-stu-id="0e79a-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="0e79a-127">Para obtener más información, vea la sección [Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="0e79a-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="0e79a-128">Componente `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="0e79a-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="0e79a-129">Los tipos en <xref:Microsoft.AspNetCore.Components.RenderTree> permiten el procesamiento de los *resultados* de las operaciones de representación.</span><span class="sxs-lookup"><span data-stu-id="0e79a-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="0e79a-130">Estos son los detalles internos de la implementación del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="0e79a-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="0e79a-131">Estos tipos se deben considerar *inestables* y deben estar sujetos a cambios en versiones futuras.</span><span class="sxs-lookup"><span data-stu-id="0e79a-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="0e79a-132">Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="0e79a-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="0e79a-133">Los archivos de componentes Razor (`.razor`) siempre se compilan.</span><span class="sxs-lookup"><span data-stu-id="0e79a-133">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="0e79a-134">La compilación es una ventaja potencial sobre la interpretación de código porque el paso de compilación se puede usar para insertar información que mejore el rendimiento de las aplicaciones en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="0e79a-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="0e79a-135">Un ejemplo clave de estas mejoras implica *números de secuencia*.</span><span class="sxs-lookup"><span data-stu-id="0e79a-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="0e79a-136">Los números de secuencia indican al runtime qué resultados proceden a partir de qué líneas de código distintas y ordenadas.</span><span class="sxs-lookup"><span data-stu-id="0e79a-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="0e79a-137">El runtime utiliza esta información con el fin de generar diferencias de árbol eficientes en tiempo lineal, que es mucho más rápido de lo que normalmente es posible para un algoritmo general de diferencia de árboles.</span><span class="sxs-lookup"><span data-stu-id="0e79a-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="0e79a-138">Considere el componente Razor siguiente (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="0e79a-138">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="0e79a-139">El código anterior se compila en algo similar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="0e79a-140">Cuando el código se ejecuta por primera vez, si `someFlag` es `true`, el generador recibe lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="0e79a-141">Secuencia</span><span class="sxs-lookup"><span data-stu-id="0e79a-141">Sequence</span></span> | <span data-ttu-id="0e79a-142">Tipo</span><span class="sxs-lookup"><span data-stu-id="0e79a-142">Type</span></span>      | <span data-ttu-id="0e79a-143">Datos</span><span class="sxs-lookup"><span data-stu-id="0e79a-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="0e79a-144">0</span><span class="sxs-lookup"><span data-stu-id="0e79a-144">0</span></span>        | <span data-ttu-id="0e79a-145">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-145">Text node</span></span> | <span data-ttu-id="0e79a-146">First</span><span class="sxs-lookup"><span data-stu-id="0e79a-146">First</span></span>  |
| <span data-ttu-id="0e79a-147">1</span><span class="sxs-lookup"><span data-stu-id="0e79a-147">1</span></span>        | <span data-ttu-id="0e79a-148">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-148">Text node</span></span> | <span data-ttu-id="0e79a-149">Second</span><span class="sxs-lookup"><span data-stu-id="0e79a-149">Second</span></span> |

<span data-ttu-id="0e79a-150">Imagine que `someFlag` se convierte en `false` y que el marcado se representa de nuevo.</span><span class="sxs-lookup"><span data-stu-id="0e79a-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="0e79a-151">Esta vez, el generador recibe lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-151">This time, the builder receives:</span></span>

| <span data-ttu-id="0e79a-152">Secuencia</span><span class="sxs-lookup"><span data-stu-id="0e79a-152">Sequence</span></span> | <span data-ttu-id="0e79a-153">Tipo</span><span class="sxs-lookup"><span data-stu-id="0e79a-153">Type</span></span>       | <span data-ttu-id="0e79a-154">Datos</span><span class="sxs-lookup"><span data-stu-id="0e79a-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="0e79a-155">1</span><span class="sxs-lookup"><span data-stu-id="0e79a-155">1</span></span>        | <span data-ttu-id="0e79a-156">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-156">Text node</span></span>  | <span data-ttu-id="0e79a-157">Second</span><span class="sxs-lookup"><span data-stu-id="0e79a-157">Second</span></span> |

<span data-ttu-id="0e79a-158">Cuando el runtime realiza una diferencia, se ve que se ha quitado el elemento en la secuencia `0`, por lo que genera el *script de edición* trivial siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="0e79a-159">Quite el primer nodo de texto.</span><span class="sxs-lookup"><span data-stu-id="0e79a-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="0e79a-160">Problema con la generación de números de secuencia mediante programación</span><span class="sxs-lookup"><span data-stu-id="0e79a-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="0e79a-161">Imagine que se ha escrito la siguiente lógica del generador de árboles de representación:</span><span class="sxs-lookup"><span data-stu-id="0e79a-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="0e79a-162">Ahora, la primera salida es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-162">Now, the first output is:</span></span>

| <span data-ttu-id="0e79a-163">Secuencia</span><span class="sxs-lookup"><span data-stu-id="0e79a-163">Sequence</span></span> | <span data-ttu-id="0e79a-164">Tipo</span><span class="sxs-lookup"><span data-stu-id="0e79a-164">Type</span></span>      | <span data-ttu-id="0e79a-165">Datos</span><span class="sxs-lookup"><span data-stu-id="0e79a-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="0e79a-166">0</span><span class="sxs-lookup"><span data-stu-id="0e79a-166">0</span></span>        | <span data-ttu-id="0e79a-167">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-167">Text node</span></span> | <span data-ttu-id="0e79a-168">First</span><span class="sxs-lookup"><span data-stu-id="0e79a-168">First</span></span>  |
| <span data-ttu-id="0e79a-169">1</span><span class="sxs-lookup"><span data-stu-id="0e79a-169">1</span></span>        | <span data-ttu-id="0e79a-170">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-170">Text node</span></span> | <span data-ttu-id="0e79a-171">Second</span><span class="sxs-lookup"><span data-stu-id="0e79a-171">Second</span></span> |

<span data-ttu-id="0e79a-172">Este resultado es idéntico al del caso anterior, por lo que no existen incidencias negativas.</span><span class="sxs-lookup"><span data-stu-id="0e79a-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="0e79a-173">`someFlag` es `false` en la segunda representación y la salida es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="0e79a-174">Secuencia</span><span class="sxs-lookup"><span data-stu-id="0e79a-174">Sequence</span></span> | <span data-ttu-id="0e79a-175">Tipo</span><span class="sxs-lookup"><span data-stu-id="0e79a-175">Type</span></span>      | <span data-ttu-id="0e79a-176">Datos</span><span class="sxs-lookup"><span data-stu-id="0e79a-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="0e79a-177">0</span><span class="sxs-lookup"><span data-stu-id="0e79a-177">0</span></span>        | <span data-ttu-id="0e79a-178">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="0e79a-178">Text node</span></span> | <span data-ttu-id="0e79a-179">Second</span><span class="sxs-lookup"><span data-stu-id="0e79a-179">Second</span></span> |

<span data-ttu-id="0e79a-180">Esta vez, el algoritmo de diferencia observa que se han producido *dos* cambios y genera el siguiente script de edición:</span><span class="sxs-lookup"><span data-stu-id="0e79a-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="0e79a-181">Cambie el valor del primer nodo de texto a `Second`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="0e79a-182">Quite el segundo nodo de texto.</span><span class="sxs-lookup"><span data-stu-id="0e79a-182">Remove the second text node.</span></span>

<span data-ttu-id="0e79a-183">La generación de los números de secuencia ha perdido toda la información útil sobre el lugar en el que las ramas `if/else` y los bucles estaban presentes en el código original.</span><span class="sxs-lookup"><span data-stu-id="0e79a-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="0e79a-184">Esto da como resultado una diferencia **dos veces más larga** que antes.</span><span class="sxs-lookup"><span data-stu-id="0e79a-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="0e79a-185">Este es un ejemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="0e79a-185">This is a trivial example.</span></span> <span data-ttu-id="0e79a-186">En casos más realistas con estructuras complejas y profundamente anidadas, y especialmente con bucles, el costo de rendimiento suele ser mayor.</span><span class="sxs-lookup"><span data-stu-id="0e79a-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="0e79a-187">En lugar de identificar inmediatamente los bloques o ramas de bucle que se han insertado o quitado, el algoritmo de diferencia tiene que recorrer profundamente los árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="0e79a-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="0e79a-188">Esto suele dar lugar a tener que compilar scripts de edición más largos, ya que el algoritmo de diferencia está desinformado sobre cómo se relacionan entre sí las estructuras antiguas y nuevas.</span><span class="sxs-lookup"><span data-stu-id="0e79a-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="0e79a-189">Guía y conclusiones</span><span class="sxs-lookup"><span data-stu-id="0e79a-189">Guidance and conclusions</span></span>

* <span data-ttu-id="0e79a-190">El rendimiento de la aplicación se ve afectado si los números de secuencia se generan dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="0e79a-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="0e79a-191">El marco no puede crear sus propios números de secuencia automáticamente en tiempo de ejecución porque la información necesaria no existe a menos que se capture en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="0e79a-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="0e79a-192">No escriba bloques grandes de lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> implementada de forma manual.</span><span class="sxs-lookup"><span data-stu-id="0e79a-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="0e79a-193">Dé preferencia a los archivos `.razor` y permita que el compilador trate los números de secuencia.</span><span class="sxs-lookup"><span data-stu-id="0e79a-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="0e79a-194">Si no puede evitar la lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> manual, divida bloques grandes de código en fragmentos más pequeños encapsulados en llamadas <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>.</span><span class="sxs-lookup"><span data-stu-id="0e79a-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="0e79a-195">Cada región tiene su propio espacio independiente de números de secuencia, por lo que puede reiniciar desde cero (o cualquier otro número arbitrario) en cada región.</span><span class="sxs-lookup"><span data-stu-id="0e79a-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="0e79a-196">Si los números de secuencia están codificados, el algoritmo de diferencia solo requiere que los números de secuencia aumenten de valor.</span><span class="sxs-lookup"><span data-stu-id="0e79a-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="0e79a-197">El valor inicial y los intervalos son irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="0e79a-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="0e79a-198">Una opción legítima es usar el número de línea de código como el número de secuencia, o comenzar a partir de cero y aumentar en unos o cientos (o cualquier intervalo preferido).</span><span class="sxs-lookup"><span data-stu-id="0e79a-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="0e79a-199">Blazor utiliza los números de secuencia, mientras que otros marcos de la interfaz de usuario de diferencia de árboles no.</span><span class="sxs-lookup"><span data-stu-id="0e79a-199">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="0e79a-200">La comparación es mucho más rápida cuando se utilizan números de secuencia, y la ventaja de Blazor es un paso de compilación que trata los números de secuencia automáticamente para desarrolladores que crean archivos `.razor`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a><span data-ttu-id="0e79a-201">Realización de transferencias de datos grandes en aplicaciones Blazor Server</span><span class="sxs-lookup"><span data-stu-id="0e79a-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="0e79a-202">En algunos escenarios, se deben transferir cantidades grandes de datos entre JavaScript y Blazor.</span><span class="sxs-lookup"><span data-stu-id="0e79a-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="0e79a-203">Normalmente, se producen transferencias de datos grandes cuando:</span><span class="sxs-lookup"><span data-stu-id="0e79a-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="0e79a-204">Las API del sistema de archivos del explorador se usan para cargar o descargar un archivo.</span><span class="sxs-lookup"><span data-stu-id="0e79a-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="0e79a-205">Se requiere la interoperabilidad con una biblioteca de terceros.</span><span class="sxs-lookup"><span data-stu-id="0e79a-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="0e79a-206">En Blazor Server, existe una limitación para evitar el paso de mensajes únicos de gran tamaño que pueden dar lugar a incidencias de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="0e79a-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="0e79a-207">Tenga en cuenta la guía siguiente al desarrollar código que transfiera datos entre JavaScript y Blazor:</span><span class="sxs-lookup"><span data-stu-id="0e79a-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="0e79a-208">Segmente los datos en partes más pequeñas y envíe los segmentos de datos secuencialmente hasta que el servidor reciba todos los datos.</span><span class="sxs-lookup"><span data-stu-id="0e79a-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="0e79a-209">No asigne objetos grandes en código JavaScript y C#.</span><span class="sxs-lookup"><span data-stu-id="0e79a-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="0e79a-210">No bloquee el subproceso de interfaz de usuario principal durante períodos largos al enviar o recibir datos.</span><span class="sxs-lookup"><span data-stu-id="0e79a-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="0e79a-211">Libere la memoria consumida al completar o cancelar el proceso.</span><span class="sxs-lookup"><span data-stu-id="0e79a-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="0e79a-212">Aplique los requisitos adicionales siguientes por motivos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="0e79a-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="0e79a-213">Declare el tamaño máximo del archivo o los datos que se pueden pasar.</span><span class="sxs-lookup"><span data-stu-id="0e79a-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="0e79a-214">Declare la tasa mínima de carga desde el cliente al servidor.</span><span class="sxs-lookup"><span data-stu-id="0e79a-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="0e79a-215">Después de que el servidor reciba los datos, los datos se pueden:</span><span class="sxs-lookup"><span data-stu-id="0e79a-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="0e79a-216">Almacenar temporalmente en un búfer de memoria hasta que se recopilen todos los segmentos.</span><span class="sxs-lookup"><span data-stu-id="0e79a-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="0e79a-217">Consumir inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="0e79a-217">Consumed immediately.</span></span> <span data-ttu-id="0e79a-218">Por ejemplo, los datos se pueden almacenar inmediatamente en una base de datos o escribir en el disco a medida que se reciba cada segmento.</span><span class="sxs-lookup"><span data-stu-id="0e79a-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="0e79a-219">La clase de cargador de archivos siguiente controla la interoperabilidad de JS con el cliente.</span><span class="sxs-lookup"><span data-stu-id="0e79a-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="0e79a-220">La clase de cargador usa la interoperabilidad de JS para realizar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e79a-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="0e79a-221">Sondear al cliente para enviar un segmento de datos.</span><span class="sxs-lookup"><span data-stu-id="0e79a-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="0e79a-222">Anular la transacción si se agota el tiempo de espera del sondeo.</span><span class="sxs-lookup"><span data-stu-id="0e79a-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="0e79a-223">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="0e79a-223">In the preceding example:</span></span>

* <span data-ttu-id="0e79a-224">`maxBase64SegmentSize` se establece en `8192`, que se calcula a partir de `maxBase64SegmentSize = segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="0e79a-225">Las API de administración de memoria de .NET Core de bajo nivel se usan para almacenar los segmentos de memoria en el servidor en `uploadedSegments`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="0e79a-226">Se usa un método `ReceiveFile` para administrar la carga a través de la interoperabilidad de JS:</span><span class="sxs-lookup"><span data-stu-id="0e79a-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="0e79a-227">El tamaño del archivo se determina en bytes a través de la interoperabilidad de JS con `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="0e79a-228">El número de segmentos que se van a recibir se calcula y almacena en `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="0e79a-229">Los segmentos se solicitan en un bucle `for` a través de la interoperabilidad de JS con `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="0e79a-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="0e79a-230">Todos los segmentos, excepto el último, deben tener 8192 bytes antes de la descodificación.</span><span class="sxs-lookup"><span data-stu-id="0e79a-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="0e79a-231">El cliente se ve obligado a enviar los datos de manera eficaz.</span><span class="sxs-lookup"><span data-stu-id="0e79a-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="0e79a-232">Para cada segmento recibido, se realizan comprobaciones antes de la descodificación con <xref:System.Convert.TryFromBase64String%2A>.</span><span class="sxs-lookup"><span data-stu-id="0e79a-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="0e79a-233">Se devuelve una secuencia con los datos como un elemento <xref:System.IO.Stream> nuevo (`SegmentedStream`) cuando se completa la carga.</span><span class="sxs-lookup"><span data-stu-id="0e79a-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="0e79a-234">La clase de la secuencia segmentada expone la lista de segmentos como un elemento <xref:System.IO.Stream> de solo lectura que no se puede buscar:</span><span class="sxs-lookup"><span data-stu-id="0e79a-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="0e79a-235">El código siguiente implementa las funciones de JavaScript para recibir los datos:</span><span class="sxs-lookup"><span data-stu-id="0e79a-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
