---
title: Escenarios avanzados de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre escenarios avanzados en Blazor, incluido cómo incorporar la lógica RenderTreeBuilder manual en una aplicación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280848"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="1f6df-103">Escenarios avanzados de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1f6df-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="1f6df-104">Controlador de circuito de Blazor Server</span><span class="sxs-lookup"><span data-stu-id="1f6df-104">Blazor Server circuit handler</span></span>

<span data-ttu-id="1f6df-105">Blazor Server permite que el código defina un *controlador de circuito*, que permite ejecutar el código en los cambios realizados en el estado del circuito de un usuario.</span><span class="sxs-lookup"><span data-stu-id="1f6df-105">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="1f6df-106">Un controlador de circuito se implementa derivando de `CircuitHandler` y registrando la clase en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f6df-106">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="1f6df-107">El ejemplo siguiente de un controlador de circuito realiza un seguimiento de las conexiones abiertas de SignalR:</span><span class="sxs-lookup"><span data-stu-id="1f6df-107">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="1f6df-108">Los controladores de circuito se registran mediante DI.</span><span class="sxs-lookup"><span data-stu-id="1f6df-108">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="1f6df-109">Las instancias con ámbito se crean por instancia de un circuito.</span><span class="sxs-lookup"><span data-stu-id="1f6df-109">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="1f6df-110">Mediante el uso de `TrackingCircuitHandler` en el ejemplo anterior, se crea un servicio singleton porque se debe realizar un seguimiento del estado de todos los circuitos:</span><span class="sxs-lookup"><span data-stu-id="1f6df-110">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="1f6df-111">Si los métodos de un controlador de circuito personalizado producen una excepción no controlada, la excepción es grave para el circuito de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="1f6df-111">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="1f6df-112">Para tolerar excepciones en el código de un controlador o en los métodos llamados, encapsule el código en una o varias instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.</span><span class="sxs-lookup"><span data-stu-id="1f6df-112">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="1f6df-113">Cuando un circuito finaliza porque un usuario se ha desconectado y el marco está limpiando el estado del circuito, el marco desecha el ámbito de DI del circuito.</span><span class="sxs-lookup"><span data-stu-id="1f6df-113">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="1f6df-114">Al desechar el ámbito, se eliminan también todos los servicios de DI con ámbito del circuito que implementan <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1f6df-114">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="1f6df-115">Si un servicio de DI produce una excepción no controlada durante la eliminación, el marco registra la excepción.</span><span class="sxs-lookup"><span data-stu-id="1f6df-115">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1f6df-116">Lógica Manual RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="1f6df-116">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1f6df-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> proporciona métodos para manipular componentes y elementos, incluida la compilación manual de componentes en código de C#.</span><span class="sxs-lookup"><span data-stu-id="1f6df-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1f6df-118">El uso de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para crear componentes es un escenario avanzado.</span><span class="sxs-lookup"><span data-stu-id="1f6df-118">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="1f6df-119">Un componente con formato incorrecto (por ejemplo, una etiqueta de marcado sin cerrar) puede dar como resultado un comportamiento indefinido.</span><span class="sxs-lookup"><span data-stu-id="1f6df-119">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1f6df-120">Tenga en cuenta el componente `PetDetails` siguiente, que se puede integrar manualmente en otro componente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-120">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1f6df-121">En el ejemplo siguiente, el bucle en el método `CreateComponent` genera tres componentes `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="1f6df-121">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1f6df-122">En métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> con un número de secuencia, los números de secuencia son números de línea de código fuente.</span><span class="sxs-lookup"><span data-stu-id="1f6df-122">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1f6df-123">El algoritmo de diferencia Blazor se basa en los números de secuencia correspondientes a líneas de código distintas, no a invocaciones de llamada distintas.</span><span class="sxs-lookup"><span data-stu-id="1f6df-123">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1f6df-124">Al crear un componente con métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, codifique los argumentos para los números de secuencia.</span><span class="sxs-lookup"><span data-stu-id="1f6df-124">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1f6df-125">**El uso de un cálculo o un contador para generar el número de secuencia puede dar lugar a un rendimiento deficiente.**</span><span class="sxs-lookup"><span data-stu-id="1f6df-125">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1f6df-126">Para obtener más información, vea la sección [Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="1f6df-126">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1f6df-127">Componente `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="1f6df-127">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="1f6df-128">Los tipos en <xref:Microsoft.AspNetCore.Components.RenderTree> permiten el procesamiento de los *resultados* de las operaciones de representación.</span><span class="sxs-lookup"><span data-stu-id="1f6df-128">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1f6df-129">Estos son los detalles internos de la implementación del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="1f6df-129">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1f6df-130">Estos tipos se deben considerar *inestables* y deben estar sujetos a cambios en versiones futuras.</span><span class="sxs-lookup"><span data-stu-id="1f6df-130">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1f6df-131">Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="1f6df-131">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="1f6df-132">Los archivos de componentes Razor (`.razor`) siempre se compilan.</span><span class="sxs-lookup"><span data-stu-id="1f6df-132">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="1f6df-133">La compilación es una ventaja potencial sobre la interpretación de código porque el paso de compilación se puede usar para insertar información que mejore el rendimiento de las aplicaciones en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="1f6df-133">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="1f6df-134">Un ejemplo clave de estas mejoras implica *números de secuencia*.</span><span class="sxs-lookup"><span data-stu-id="1f6df-134">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="1f6df-135">Los números de secuencia indican al runtime qué resultados proceden a partir de qué líneas de código distintas y ordenadas.</span><span class="sxs-lookup"><span data-stu-id="1f6df-135">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1f6df-136">El runtime utiliza esta información con el fin de generar diferencias de árbol eficientes en tiempo lineal, que es mucho más rápido de lo que normalmente es posible para un algoritmo general de diferencia de árboles.</span><span class="sxs-lookup"><span data-stu-id="1f6df-136">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1f6df-137">Considere el componente Razor siguiente (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="1f6df-137">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1f6df-138">El código anterior se compila en algo similar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-138">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1f6df-139">Cuando el código se ejecuta por primera vez, si `someFlag` es `true`, el generador recibe lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-139">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1f6df-140">Secuencia</span><span class="sxs-lookup"><span data-stu-id="1f6df-140">Sequence</span></span> | <span data-ttu-id="1f6df-141">Tipo</span><span class="sxs-lookup"><span data-stu-id="1f6df-141">Type</span></span>      | <span data-ttu-id="1f6df-142">Datos</span><span class="sxs-lookup"><span data-stu-id="1f6df-142">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1f6df-143">0</span><span class="sxs-lookup"><span data-stu-id="1f6df-143">0</span></span>        | <span data-ttu-id="1f6df-144">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-144">Text node</span></span> | <span data-ttu-id="1f6df-145">First</span><span class="sxs-lookup"><span data-stu-id="1f6df-145">First</span></span>  |
| <span data-ttu-id="1f6df-146">1</span><span class="sxs-lookup"><span data-stu-id="1f6df-146">1</span></span>        | <span data-ttu-id="1f6df-147">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-147">Text node</span></span> | <span data-ttu-id="1f6df-148">Second</span><span class="sxs-lookup"><span data-stu-id="1f6df-148">Second</span></span> |

<span data-ttu-id="1f6df-149">Imagine que `someFlag` se convierte en `false` y que el marcado se representa de nuevo.</span><span class="sxs-lookup"><span data-stu-id="1f6df-149">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1f6df-150">Esta vez, el generador recibe lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-150">This time, the builder receives:</span></span>

| <span data-ttu-id="1f6df-151">Secuencia</span><span class="sxs-lookup"><span data-stu-id="1f6df-151">Sequence</span></span> | <span data-ttu-id="1f6df-152">Tipo</span><span class="sxs-lookup"><span data-stu-id="1f6df-152">Type</span></span>       | <span data-ttu-id="1f6df-153">Datos</span><span class="sxs-lookup"><span data-stu-id="1f6df-153">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1f6df-154">1</span><span class="sxs-lookup"><span data-stu-id="1f6df-154">1</span></span>        | <span data-ttu-id="1f6df-155">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-155">Text node</span></span>  | <span data-ttu-id="1f6df-156">Second</span><span class="sxs-lookup"><span data-stu-id="1f6df-156">Second</span></span> |

<span data-ttu-id="1f6df-157">Cuando el runtime realiza una diferencia, se ve que se ha quitado el elemento en la secuencia `0`, por lo que genera el *script de edición* trivial siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-157">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1f6df-158">Quite el primer nodo de texto.</span><span class="sxs-lookup"><span data-stu-id="1f6df-158">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="1f6df-159">Problema con la generación de números de secuencia mediante programación</span><span class="sxs-lookup"><span data-stu-id="1f6df-159">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="1f6df-160">Imagine que se ha escrito la siguiente lógica del generador de árboles de representación:</span><span class="sxs-lookup"><span data-stu-id="1f6df-160">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1f6df-161">Ahora, la primera salida es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-161">Now, the first output is:</span></span>

| <span data-ttu-id="1f6df-162">Secuencia</span><span class="sxs-lookup"><span data-stu-id="1f6df-162">Sequence</span></span> | <span data-ttu-id="1f6df-163">Tipo</span><span class="sxs-lookup"><span data-stu-id="1f6df-163">Type</span></span>      | <span data-ttu-id="1f6df-164">Datos</span><span class="sxs-lookup"><span data-stu-id="1f6df-164">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1f6df-165">0</span><span class="sxs-lookup"><span data-stu-id="1f6df-165">0</span></span>        | <span data-ttu-id="1f6df-166">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-166">Text node</span></span> | <span data-ttu-id="1f6df-167">First</span><span class="sxs-lookup"><span data-stu-id="1f6df-167">First</span></span>  |
| <span data-ttu-id="1f6df-168">1</span><span class="sxs-lookup"><span data-stu-id="1f6df-168">1</span></span>        | <span data-ttu-id="1f6df-169">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-169">Text node</span></span> | <span data-ttu-id="1f6df-170">Second</span><span class="sxs-lookup"><span data-stu-id="1f6df-170">Second</span></span> |

<span data-ttu-id="1f6df-171">Este resultado es idéntico al del caso anterior, por lo que no existen incidencias negativas.</span><span class="sxs-lookup"><span data-stu-id="1f6df-171">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1f6df-172">`someFlag` es `false` en la segunda representación y la salida es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f6df-172">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1f6df-173">Secuencia</span><span class="sxs-lookup"><span data-stu-id="1f6df-173">Sequence</span></span> | <span data-ttu-id="1f6df-174">Tipo</span><span class="sxs-lookup"><span data-stu-id="1f6df-174">Type</span></span>      | <span data-ttu-id="1f6df-175">Datos</span><span class="sxs-lookup"><span data-stu-id="1f6df-175">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1f6df-176">0</span><span class="sxs-lookup"><span data-stu-id="1f6df-176">0</span></span>        | <span data-ttu-id="1f6df-177">Nodo de texto</span><span class="sxs-lookup"><span data-stu-id="1f6df-177">Text node</span></span> | <span data-ttu-id="1f6df-178">Second</span><span class="sxs-lookup"><span data-stu-id="1f6df-178">Second</span></span> |

<span data-ttu-id="1f6df-179">Esta vez, el algoritmo de diferencia observa que se han producido *dos* cambios y genera el siguiente script de edición:</span><span class="sxs-lookup"><span data-stu-id="1f6df-179">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1f6df-180">Cambie el valor del primer nodo de texto a `Second`.</span><span class="sxs-lookup"><span data-stu-id="1f6df-180">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1f6df-181">Quite el segundo nodo de texto.</span><span class="sxs-lookup"><span data-stu-id="1f6df-181">Remove the second text node.</span></span>

<span data-ttu-id="1f6df-182">La generación de los números de secuencia ha perdido toda la información útil sobre el lugar en el que las ramas `if/else` y los bucles estaban presentes en el código original.</span><span class="sxs-lookup"><span data-stu-id="1f6df-182">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1f6df-183">Esto da como resultado una diferencia **dos veces más larga** que antes.</span><span class="sxs-lookup"><span data-stu-id="1f6df-183">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1f6df-184">Este es un ejemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="1f6df-184">This is a trivial example.</span></span> <span data-ttu-id="1f6df-185">En casos más realistas con estructuras complejas y profundamente anidadas, y especialmente con bucles, el costo de rendimiento suele ser mayor.</span><span class="sxs-lookup"><span data-stu-id="1f6df-185">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="1f6df-186">En lugar de identificar inmediatamente los bloques o ramas de bucle que se han insertado o quitado, el algoritmo de diferencia tiene que recorrer profundamente los árboles de representación.</span><span class="sxs-lookup"><span data-stu-id="1f6df-186">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="1f6df-187">Esto suele dar lugar a tener que compilar scripts de edición más largos, ya que el algoritmo de diferencia está desinformado sobre cómo se relacionan entre sí las estructuras antiguas y nuevas.</span><span class="sxs-lookup"><span data-stu-id="1f6df-187">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="1f6df-188">Guía y conclusiones</span><span class="sxs-lookup"><span data-stu-id="1f6df-188">Guidance and conclusions</span></span>

* <span data-ttu-id="1f6df-189">El rendimiento de la aplicación se ve afectado si los números de secuencia se generan dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="1f6df-189">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1f6df-190">El marco no puede crear sus propios números de secuencia automáticamente en tiempo de ejecución porque la información necesaria no existe a menos que se capture en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="1f6df-190">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1f6df-191">No escriba bloques grandes de lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> implementada de forma manual.</span><span class="sxs-lookup"><span data-stu-id="1f6df-191">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="1f6df-192">Dé preferencia a los archivos `.razor` y permita que el compilador trate los números de secuencia.</span><span class="sxs-lookup"><span data-stu-id="1f6df-192">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1f6df-193">Si no puede evitar la lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> manual, divida bloques grandes de código en fragmentos más pequeños encapsulados en llamadas <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>.</span><span class="sxs-lookup"><span data-stu-id="1f6df-193">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="1f6df-194">Cada región tiene su propio espacio independiente de números de secuencia, por lo que puede reiniciar desde cero (o cualquier otro número arbitrario) en cada región.</span><span class="sxs-lookup"><span data-stu-id="1f6df-194">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1f6df-195">Si los números de secuencia están codificados, el algoritmo de diferencia solo requiere que los números de secuencia aumenten de valor.</span><span class="sxs-lookup"><span data-stu-id="1f6df-195">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1f6df-196">El valor inicial y los intervalos son irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="1f6df-196">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1f6df-197">Una opción legítima es usar el número de línea de código como el número de secuencia, o comenzar a partir de cero y aumentar en unos o cientos (o cualquier intervalo preferido).</span><span class="sxs-lookup"><span data-stu-id="1f6df-197">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="1f6df-198">Blazor utiliza los números de secuencia, mientras que otros marcos de la interfaz de usuario de diferencia de árboles no.</span><span class="sxs-lookup"><span data-stu-id="1f6df-198">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1f6df-199">La comparación es mucho más rápida cuando se utilizan números de secuencia, y la ventaja de Blazor es un paso de compilación que trata los números de secuencia automáticamente para desarrolladores que crean archivos `.razor`.</span><span class="sxs-lookup"><span data-stu-id="1f6df-199">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
