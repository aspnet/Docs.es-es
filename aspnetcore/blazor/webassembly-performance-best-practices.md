---
title: Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core
author: pranavkm
description: Sugerencias para aumentar el rendimiento de las aplicaciones Blazor WebAssembly en ASP.NET Core y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 58a87bc5413523fdf052a9e1c41196bb8b0ab457
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529974"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="313cb-103">Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="313cb-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="313cb-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm) y [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="313cb-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="313cb-105">Blazor WebAssembly se ha diseñado y optimizado cuidadosamente para permitir un alto rendimiento en los escenarios de interfaz de usuario de aplicaciones más realistas.</span><span class="sxs-lookup"><span data-stu-id="313cb-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="313cb-106">Sin embargo, la generación de los mejores resultados depende de que los desarrolladores utilicen los patrones y características correctos.</span><span class="sxs-lookup"><span data-stu-id="313cb-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="313cb-107">Considere los aspectos siguientes:</span><span class="sxs-lookup"><span data-stu-id="313cb-107">Consider the following aspects:</span></span>

* <span data-ttu-id="313cb-108">**Rendimiento del entorno de ejecución**: el código de .NET se ejecuta en un intérprete dentro del entorno de ejecución de WebAssembly, por lo que el rendimiento de la CPU es limitado.</span><span class="sxs-lookup"><span data-stu-id="313cb-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="313cb-109">En escenarios exigentes, la aplicación se beneficia de la [optimización de la velocidad de representación](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="313cb-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="313cb-110">**Tiempo de inicio**: la aplicación transfiere un entorno de ejecución de .NET al explorador; por ello, es importante usar características que [minimizan el tamaño de descarga de la aplicación](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="313cb-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="313cb-111">Optimización de la velocidad de representación</span><span class="sxs-lookup"><span data-stu-id="313cb-111">Optimize rendering speed</span></span>

<span data-ttu-id="313cb-112">En las secciones siguientes se proporcionan recomendaciones para minimizar la carga de trabajo de representación y mejorar la capacidad de respuesta de la IU.</span><span class="sxs-lookup"><span data-stu-id="313cb-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="313cb-113">Si sigue este consejo, podría *multiplicar por diez o más* las velocidades de representación de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="313cb-114">Evitación de la representación innecesaria de subárboles de componentes</span><span class="sxs-lookup"><span data-stu-id="313cb-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="313cb-115">En el entorno de ejecución, los componentes existen como una jerarquía.</span><span class="sxs-lookup"><span data-stu-id="313cb-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="313cb-116">Un componente raíz tiene componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="313cb-116">A root component has child components.</span></span> <span data-ttu-id="313cb-117">A su vez, los elementos secundarios de la raíz tienen sus propios componentes secundarios, etc.</span><span class="sxs-lookup"><span data-stu-id="313cb-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="313cb-118">Cuando se produce un evento, como la selección de un botón por un usuario, este es el modo en que Blazor decide qué componentes se representarán:</span><span class="sxs-lookup"><span data-stu-id="313cb-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

1. <span data-ttu-id="313cb-119">El propio evento se envía a cualquier componente que represente el controlador del evento.</span><span class="sxs-lookup"><span data-stu-id="313cb-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="313cb-120">Después de ejecutar el controlador de eventos, ese componente se volverá a representar.</span><span class="sxs-lookup"><span data-stu-id="313cb-120">After executing the event handler, that component is rerendered.</span></span>
1. <span data-ttu-id="313cb-121">Siempre que se vuelve a representar cualquier componente, este proporciona una nueva copia de los valores de los parámetros a cada uno de sus componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="313cb-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
1. <span data-ttu-id="313cb-122">Al recibir un nuevo conjunto de valores de parámetros, cada componente elige si desea volver a representarlo.</span><span class="sxs-lookup"><span data-stu-id="313cb-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="313cb-123">De forma predeterminada, los componentes se vuelven a representar si los valores de los parámetros pueden haber cambiado (por ejemplo, si son objetos mutables).</span><span class="sxs-lookup"><span data-stu-id="313cb-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="313cb-124">Los dos últimos pasos de esta secuencia continúan de forma recursiva hacia abajo en la jerarquía de componentes.</span><span class="sxs-lookup"><span data-stu-id="313cb-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="313cb-125">En muchos casos, se volverá a representar el subárbol completo.</span><span class="sxs-lookup"><span data-stu-id="313cb-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="313cb-126">Esto significa que los eventos que tienen como destino componentes de alto nivel pueden provocar procesos de nueva representación costosos, ya que todo lo que se encuentra por debajo de ese punto debe volver a representarse.</span><span class="sxs-lookup"><span data-stu-id="313cb-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="313cb-127">Si desea interrumpir este proceso y evitar la representación de la recursividad en un subárbol en particular, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="313cb-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

* <span data-ttu-id="313cb-128">Asegúrese de que todos los parámetros de un determinado componente son de tipos primitivos inmutables (por ejemplo, `string`, `int`, `bool`, `DateTime` y otros).</span><span class="sxs-lookup"><span data-stu-id="313cb-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="313cb-129">La lógica integrada para detectar cambios omite automáticamente la nueva representación si ninguno de estos valores de los parámetros ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="313cb-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="313cb-130">Si representa un componente secundario con `<Customer CustomerId="@item.CustomerId" />`, donde `CustomerId` es un valor de `int`, no se volverá a representar excepto cuando `item.CustomerId` cambie.</span><span class="sxs-lookup"><span data-stu-id="313cb-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
* <span data-ttu-id="313cb-131">Si necesita aceptar valores de parámetros no primitivos (por ejemplo, tipos de modelos personalizados), devoluciones de llamada de eventos o valores <xref:Microsoft.AspNetCore.Components.RenderFragment>, puede invalidar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para controlar la decisión sobre si se va a representar, lo que se describe en la sección [Uso de `ShouldRender`](#use-of-shouldrender).</span><span class="sxs-lookup"><span data-stu-id="313cb-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="313cb-132">Al omitir la nueva representación de subárboles completos, es posible que pueda suprimir la gran mayoría de los costos de representación cuando se produce un evento.</span><span class="sxs-lookup"><span data-stu-id="313cb-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="313cb-133">Es posible que desee separar componentes secundarios específicamente para poder omitir la nueva representación de esa parte de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="313cb-134">Este es un método valido para reducir el costo de representación de un componente primario.</span><span class="sxs-lookup"><span data-stu-id="313cb-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="313cb-135">Uso de `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="313cb-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="313cb-136">Si crea un componente de solo interfaz de usuario que nunca cambia tras la representación inicial (con independencia de cualquier valor de parámetros), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para que devuelva `false`:</span><span class="sxs-lookup"><span data-stu-id="313cb-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="313cb-137">Si el componente solo requiere volver a representar cuando los valores de sus parámetros cambian de manera particular, puede usar campos privados para realizar el seguimiento de la información necesaria para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="313cb-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="313cb-138">En el ejemplo siguiente, `shouldRender` se basa en la comprobación de cualquier tipo de cambio o mutación que deba provocar una nueva representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="313cb-139">`prevOutboundFlightId` y `prevInboundFlightId` supervisan la información de la siguiente actualización posible:</span><span class="sxs-lookup"><span data-stu-id="313cb-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="313cb-140">En el código anterior, un controlador de eventos también puede establecer `shouldRender` en `true` para que el componente se vuelva a representar después del evento.</span><span class="sxs-lookup"><span data-stu-id="313cb-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="313cb-141">Para la mayoría de los componentes, este nivel de control manual no es necesario.</span><span class="sxs-lookup"><span data-stu-id="313cb-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="313cb-142">Solo debe preocuparse por omitir los subárboles de representación si esos subárboles tienen una representación especialmente costosa y están causando un retraso en la IU.</span><span class="sxs-lookup"><span data-stu-id="313cb-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="313cb-143">Para más información, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="313cb-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="313cb-144">Virtualización</span><span class="sxs-lookup"><span data-stu-id="313cb-144">Virtualization</span></span>

<span data-ttu-id="313cb-145">Al representar grandes cantidades de interfaz de usuario dentro de un bucle, por ejemplo, una lista o una cuadrícula con miles de entradas, la gran cantidad de operaciones de representación puede provocar un retraso en la representación de la interfaz de usuario y, por tanto, una mala experiencia del usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="313cb-146">Dado que el usuario solo puede ver un pequeño número de elementos a la vez sin desplazarse, parece que no vale la pena dedicar tanto tiempo a representar elementos que no son visibles actualmente.</span><span class="sxs-lookup"><span data-stu-id="313cb-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="313cb-147">Para solucionar esto, Blazor proporciona el componente `Virtualize` que crea los comportamientos de apariencia y desplazamiento de una lista arbitrariamente grande, pero que solo representa los elementos de lista que están dentro de la ventanilla de desplazamiento actual.</span><span class="sxs-lookup"><span data-stu-id="313cb-147">To address this, Blazor provides the `Virtualize` component that creates the appearance and scroll behaviors of an arbitrarily-large list but only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="313cb-148">Por ejemplo, esto significa que la aplicación puede tener una lista con 100 000 entradas, pero solo debe pagar el costo de representación de 20 elementos que son visibles a la vez.</span><span class="sxs-lookup"><span data-stu-id="313cb-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="313cb-149">El uso del componente `Virtualize` puede escalar verticalmente el rendimiento de la interfaz de usuario en órdenes de magnitud.</span><span class="sxs-lookup"><span data-stu-id="313cb-149">Use of the `Virtualize` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="313cb-150">Para más información, consulte <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="313cb-150">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="313cb-151">Creación de componentes ligeros y optimizados</span><span class="sxs-lookup"><span data-stu-id="313cb-151">Create lightweight, optimized components</span></span>

<span data-ttu-id="313cb-152">La mayoría de los componentes de Blazor no requieren esfuerzos de optimización agresivos.</span><span class="sxs-lookup"><span data-stu-id="313cb-152">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="313cb-153">Esto se debe a que la mayoría de los componentes no se repiten a menudo en la interfaz de usuario y no se vuelven a representar con alta frecuencia.</span><span class="sxs-lookup"><span data-stu-id="313cb-153">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="313cb-154">Por ejemplo, los componentes `@page` y los componentes que representan partes de la interfaz de usuario de alto nivel como cuadros de diálogo o formularios, lo más probable es que solo aparezcan de uno en uno y que solo se vuelvan a representar en respuesta a un gesto del usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-154">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="313cb-155">Estos componentes no suponen una gran carga de trabajo de representación, por lo que puede usar libremente cualquier combinación de características del marco que desee sin preocuparse demasiado por el rendimiento de la representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-155">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="313cb-156">Sin embargo, también existen escenarios comunes en los que se compilan componentes que deben repetirse a gran escala.</span><span class="sxs-lookup"><span data-stu-id="313cb-156">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="313cb-157">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="313cb-157">For example:</span></span>

* <span data-ttu-id="313cb-158">Los formularios anidados grandes pueden tener cientos de entradas individuales, etiquetas y otros elementos.</span><span class="sxs-lookup"><span data-stu-id="313cb-158">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
* <span data-ttu-id="313cb-159">Las cuadrículas pueden tener miles de celdas.</span><span class="sxs-lookup"><span data-stu-id="313cb-159">Grids may have thousands of cells.</span></span>
* <span data-ttu-id="313cb-160">Los gráficos de dispersión pueden tener millones de puntos de datos.</span><span class="sxs-lookup"><span data-stu-id="313cb-160">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="313cb-161">Si modela cada unidad como instancias de componente independientes, habrá tantas que el rendimiento de su representación se vuelve crítico.</span><span class="sxs-lookup"><span data-stu-id="313cb-161">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="313cb-162">En esta sección se proporcionan consejos sobre cómo crear estos componentes ligeros para que la interfaz de usuario se mantenga rápida y con capacidad de respuesta.</span><span class="sxs-lookup"><span data-stu-id="313cb-162">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="313cb-163">Evitación de miles de instancias de componentes</span><span class="sxs-lookup"><span data-stu-id="313cb-163">Avoid thousands of component instances</span></span>

<span data-ttu-id="313cb-164">Cada componente es una isla independiente que se puede representar independientemente de sus elementos primarios y secundarios.</span><span class="sxs-lookup"><span data-stu-id="313cb-164">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="313cb-165">Al elegir cómo dividir la interfaz de usuario en una jerarquía de componentes, toma el control sobre el nivel de detalle de la representación de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-165">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="313cb-166">Esto puede ser bueno o malo para el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="313cb-166">This can be either good or bad for performance.</span></span>

* <span data-ttu-id="313cb-167">Al dividir la interfaz de usuario en más componentes, puede ocurrir que se vuelvan a representar partes más pequeñas de la interfaz de usuario cuando se produzcan eventos.</span><span class="sxs-lookup"><span data-stu-id="313cb-167">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="313cb-168">Por ejemplo, cuando un usuario hace clic en un botón en una fila de la tabla, es posible que solo pueda volver a representar esa fila en lugar de toda la página o tabla.</span><span class="sxs-lookup"><span data-stu-id="313cb-168">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
* <span data-ttu-id="313cb-169">Sin embargo, cada componente adicional implica algo de memoria adicional y sobrecarga de CPU para tratar con su estado independiente y su ciclo de vida de representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-169">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="313cb-170">Al optimizar el rendimiento de Blazor WebAssembly en .NET 5, se midió una sobrecarga de representación de alrededor de 0,06 ms por instancia de componente.</span><span class="sxs-lookup"><span data-stu-id="313cb-170">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="313cb-171">Esto se basa en un componente simple que acepta tres parámetros que se ejecutan en un equipo portátil típico.</span><span class="sxs-lookup"><span data-stu-id="313cb-171">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="313cb-172">Internamente, la sobrecarga se debe en gran parte a la recuperación del estado de cada componente de los diccionarios y al paso y recepción de parámetros.</span><span class="sxs-lookup"><span data-stu-id="313cb-172">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="313cb-173">Mediante la multiplicación, puede ver que agregar 2000 instancias de componentes adicionales agregaría 0,12 segundos al tiempo de representación y la interfaz de usuario comenzaría a ser lenta para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="313cb-173">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="313cb-174">Es posible hacer que los componentes sean más ligeros para que pueda tener mayor cantidad, pero con frecuencia resulta más eficaz no tener tantos componentes.</span><span class="sxs-lookup"><span data-stu-id="313cb-174">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="313cb-175">Estas dos opciones se describen en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="313cb-175">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="313cb-176">Componentes secundarios insertados en sus elementos primarios</span><span class="sxs-lookup"><span data-stu-id="313cb-176">Inline child components into their parents</span></span>

<span data-ttu-id="313cb-177">Piense en el siguiente componente que representa una secuencia de componentes secundarios:</span><span class="sxs-lookup"><span data-stu-id="313cb-177">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="313cb-178">En el código de ejemplo anterior, el componente `<ChatMessageDisplay>` se define en un archivo `ChatMessageDisplay.razor` que contiene:</span><span class="sxs-lookup"><span data-stu-id="313cb-178">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="313cb-179">El ejemplo anterior funciona bien y se ejecuta correctamente siempre y cuando no se muestren miles de mensajes a la vez.</span><span class="sxs-lookup"><span data-stu-id="313cb-179">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="313cb-180">Para mostrar miles de mensajes a la vez, considere la posibilidad de *no* separar el componente `ChatMessageDisplay` independiente.</span><span class="sxs-lookup"><span data-stu-id="313cb-180">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="313cb-181">En su lugar, inserte la representación directamente en el elemento primario:</span><span class="sxs-lookup"><span data-stu-id="313cb-181">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="313cb-182">De este modo, se evita la sobrecarga por componente de la representación de tantos componentes secundarios a costa de no poder volver a representarlos de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="313cb-182">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="313cb-183">Definición de `RenderFragments` reutilizables en el código</span><span class="sxs-lookup"><span data-stu-id="313cb-183">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="313cb-184">Puede que esté separando los componentes secundarios únicamente como una manera de reutilizar la lógica de representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-184">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="313cb-185">En ese caso, todavía es posible reutilizar la lógica de representación sin declarar componentes reales.</span><span class="sxs-lookup"><span data-stu-id="313cb-185">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="313cb-186">En el bloque `@code` de cualquier componente, puede definir un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment> que emita la interfaz de usuario y al que se pueda llamar desde cualquier lugar:</span><span class="sxs-lookup"><span data-stu-id="313cb-186">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    private RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="313cb-187">Como se demostró en el ejemplo anterior, los componentes pueden emitir marcado a partir del código dentro de su bloque `@code` y fuera de él.</span><span class="sxs-lookup"><span data-stu-id="313cb-187">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="313cb-188">Este enfoque define un delegado <xref:Microsoft.AspNetCore.Components.RenderFragment> que puede representar dentro de la salida de representación normal del componente, opcionalmente en varios lugares.</span><span class="sxs-lookup"><span data-stu-id="313cb-188">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="313cb-189">Es necesario que el delegado acepte un parámetro denominado `__builder` de tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para que el compilador de Razor pueda generar instrucciones de representación para él.</span><span class="sxs-lookup"><span data-stu-id="313cb-189">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="313cb-190">Si desea que sea reutilizable en varios componentes, considere la posibilidad de declararlo miembro de `public static`:</span><span class="sxs-lookup"><span data-stu-id="313cb-190">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="313cb-191">Ahora se le podría invocar desde un componente no relacionado.</span><span class="sxs-lookup"><span data-stu-id="313cb-191">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="313cb-192">Esta técnica resulta útil para crear bibliotecas de fragmentos de marcado reutilizables que se representan sin sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="313cb-192">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="313cb-193">Los delegados de <xref:Microsoft.AspNetCore.Components.RenderFragment> también pueden aceptar parámetros.</span><span class="sxs-lookup"><span data-stu-id="313cb-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="313cb-194">Para crear el equivalente del componente `ChatMessageDisplay` del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="313cb-194">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @ChatMessageDisplay(message)
    }
</div>

@code {
    private RenderFragment<ChatMessage> ChatMessageDisplay = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="313cb-195">Este enfoque proporciona la ventaja de reutilizar la lógica de representación sin la sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="313cb-195">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="313cb-196">Sin embargo, no tiene la ventaja de poder actualizar su subárbol de la interfaz de usuario de forma independiente, ni tampoco tiene la capacidad de omitir la representación de ese subárbol de la interfaz de usuario cuando su elemento primario se representa, ya que no hay ningún límite de componente.</span><span class="sxs-lookup"><span data-stu-id="313cb-196">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

<span data-ttu-id="313cb-197">Para un campo, método o propiedad no estáticos al que no pueda hacer referencia un inicializador de campo, como `TitleTemplate` en el ejemplo siguiente, use una propiedad en lugar de un campo para <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span><span class="sxs-lookup"><span data-stu-id="313cb-197">For a non-static field, method, or property that can't be referenced by a field initializer, such as `TitleTemplate` in the following example, use a property instead of a field for the <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span></span>

```csharp
protected RenderFragment DisplayTitle => __builder =>
{
    <div>
        @TitleTemplate
    </div>   
};
```

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="313cb-198">No recepción de demasiados parámetros</span><span class="sxs-lookup"><span data-stu-id="313cb-198">Don't receive too many parameters</span></span>

<span data-ttu-id="313cb-199">Si un componente se repite con mucha frecuencia, por ejemplo, cientos o miles de veces, tenga en cuenta que la sobrecarga de pasar y recibir cada parámetro aumenta.</span><span class="sxs-lookup"><span data-stu-id="313cb-199">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="313cb-200">Es raro que demasiados parámetros restrinjan gravemente el rendimiento, pero puede ser un factor.</span><span class="sxs-lookup"><span data-stu-id="313cb-200">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="313cb-201">En el caso de un componente `<TableCell>` que se represente 1000 veces dentro de una cuadrícula, cada parámetro adicional que se le pase podría agregar alrededor de 15 ms al costo total de representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-201">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="313cb-202">Si cada celda aceptara 10 parámetros, el paso de parámetros tomaría alrededor de 150 ms por representación de componente y, por lo tanto, quizás 150 000 ms (150 segundos), lo que por sí mismo causaría un retraso en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-202">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="313cb-203">Para reducir esta carga, puede agrupar varios parámetros a través de clases personalizadas.</span><span class="sxs-lookup"><span data-stu-id="313cb-203">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="313cb-204">Por ejemplo, un componente `<TableCell>` podría aceptar:</span><span class="sxs-lookup"><span data-stu-id="313cb-204">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="313cb-205">En el ejemplo anterior, `Data` es diferente para cada celda, pero `Options` es común en todas ellas.</span><span class="sxs-lookup"><span data-stu-id="313cb-205">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="313cb-206">Por supuesto, podría ser una mejora no tener un componente `<TableCell>` y, en su lugar, incorporar su lógica en el componente principal.</span><span class="sxs-lookup"><span data-stu-id="313cb-206">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="313cb-207">Garantía de que los parámetros en cascada son fijos</span><span class="sxs-lookup"><span data-stu-id="313cb-207">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="313cb-208">El componente `<CascadingValue>` tiene un parámetro opcional denominado `IsFixed`.</span><span class="sxs-lookup"><span data-stu-id="313cb-208">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

* <span data-ttu-id="313cb-209">Si el valor `IsFixed` es `false` (el valor predeterminado), cada destinatario del valor en cascada configura una suscripción para recibir notificaciones de los cambios.</span><span class="sxs-lookup"><span data-stu-id="313cb-209">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="313cb-210">En este caso, `[CascadingParameter]` es **sustancialmente más caro** que un `[Parameter]` normal debido al seguimiento de las suscripciones.</span><span class="sxs-lookup"><span data-stu-id="313cb-210">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
* <span data-ttu-id="313cb-211">Si el valor `IsFixed` es `true` (por ejemplo, `<CascadingValue Value="@someValue" IsFixed="true">`), los destinatarios reciben el valor inicial, pero *no* configuran ninguna suscripción para recibir actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="313cb-211">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="313cb-212">En este caso, cada `[CascadingParameter]` es ligero y **no es más caro** que un `[Parameter]` normal.</span><span class="sxs-lookup"><span data-stu-id="313cb-212">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="313cb-213">Por tanto, siempre que sea posible, debe usar `IsFixed="true"` en valores en cascada.</span><span class="sxs-lookup"><span data-stu-id="313cb-213">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="313cb-214">Puede hacerlo cada vez que el valor proporcionado no cambie con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="313cb-214">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="313cb-215">En el patrón común en el que un componente pasa `this` como un valor en cascada, debe utilizar `IsFixed="true"`:</span><span class="sxs-lookup"><span data-stu-id="313cb-215">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="313cb-216">Esto supone una gran diferencia si hay un gran número de componentes diferentes que reciben el valor en cascada.</span><span class="sxs-lookup"><span data-stu-id="313cb-216">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="313cb-217">Para más información, consulte <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="313cb-217">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="313cb-218">Evitación de la expansión de atributos con `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="313cb-218">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="313cb-219">Los componentes pueden optar por recibir valores de parámetro "no coincidentes" mediante la marca <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>:</span><span class="sxs-lookup"><span data-stu-id="313cb-219">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="313cb-220">Este enfoque permite pasar atributos adicionales arbitrarios al elemento.</span><span class="sxs-lookup"><span data-stu-id="313cb-220">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="313cb-221">Sin embargo, también es bastante costoso porque el representador debe:</span><span class="sxs-lookup"><span data-stu-id="313cb-221">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="313cb-222">Hacer coincidir todos los parámetros proporcionados con el conjunto de parámetros conocidos para compilar un diccionario.</span><span class="sxs-lookup"><span data-stu-id="313cb-222">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="313cb-223">Supervisar el modo en que varias copias del mismo atributo se sobrescriben entre sí.</span><span class="sxs-lookup"><span data-stu-id="313cb-223">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="313cb-224">No dude en usar <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en componentes que no sean críticos para el rendimiento, como aquellos que no se repiten con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="313cb-224">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="313cb-225">Sin embargo, para los componentes que se representan a escala, como cada elemento en una lista extensa o las celdas de una cuadrícula, intente evitar la expansión de atributos.</span><span class="sxs-lookup"><span data-stu-id="313cb-225">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="313cb-226">Para más información, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="313cb-226">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="313cb-227">Implementación manual de `SetParametersAsync`</span><span class="sxs-lookup"><span data-stu-id="313cb-227">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="313cb-228">Uno de los principales aspectos de la sobrecarga de representación por componente es escribir valores de parámetros entrantes en las propiedades de `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="313cb-228">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="313cb-229">El representador tiene que usar la reflexión para hacerlo.</span><span class="sxs-lookup"><span data-stu-id="313cb-229">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="313cb-230">Aunque esto está optimizado en cierto modo, la ausencia de compatibilidad con JIT en el entorno de ejecución de WebAssembly impone límites.</span><span class="sxs-lookup"><span data-stu-id="313cb-230">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="313cb-231">En algunos casos extremos, puede que desee evitar la reflexión e implementar su propia lógica de configuración de parámetros de forma manual.</span><span class="sxs-lookup"><span data-stu-id="313cb-231">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="313cb-232">Esto puede ser aplicable cuando:</span><span class="sxs-lookup"><span data-stu-id="313cb-232">This may be applicable when:</span></span>

* <span data-ttu-id="313cb-233">Tiene un componente que se representa con mucha frecuencia (por ejemplo, hay cientos o miles de copias suyas en la interfaz de usuario).</span><span class="sxs-lookup"><span data-stu-id="313cb-233">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
* <span data-ttu-id="313cb-234">Acepta muchos parámetros.</span><span class="sxs-lookup"><span data-stu-id="313cb-234">It accepts many parameters.</span></span>
* <span data-ttu-id="313cb-235">Observa que la sobrecarga de recibir parámetros tiene una incidencia observable en la capacidad de respuesta de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-235">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="313cb-236">En estos casos, puede invalidar el método <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> virtual del componente e implementar su propia lógica específica del componente.</span><span class="sxs-lookup"><span data-stu-id="313cb-236">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="313cb-237">En el ejemplo siguiente se evita deliberadamente cualquier búsqueda en el diccionario:</span><span class="sxs-lookup"><span data-stu-id="313cb-237">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="313cb-238">En el código anterior, la devolución de la clase base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ejecuta los métodos de ciclo de vida normales sin asignar los parámetros de nuevo.</span><span class="sxs-lookup"><span data-stu-id="313cb-238">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="313cb-239">Como puede ver en el código anterior, invalidar <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> y proporcionar lógica personalizada es complicado y laborioso, por lo que no se recomienda este enfoque en general.</span><span class="sxs-lookup"><span data-stu-id="313cb-239">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="313cb-240">En casos extremos, puede mejorar el rendimiento de la representación en un 20-25 %, pero solo debe considerar este enfoque en los escenarios mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="313cb-240">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="313cb-241">No desencadenación de eventos demasiado rápido</span><span class="sxs-lookup"><span data-stu-id="313cb-241">Don't trigger events too rapidly</span></span>

<span data-ttu-id="313cb-242">Algunos eventos del navegador se activan con mucha frecuencia, por ejemplo, `onmousemove` y `onscroll`, que pueden activarse decenas o cientos de veces por segundo.</span><span class="sxs-lookup"><span data-stu-id="313cb-242">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="313cb-243">En la mayoría de los casos, no es necesario realizar actualizaciones de la interfaz de usuario con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="313cb-243">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="313cb-244">Si intenta hacerlo, puede dañar la capacidad de respuesta de la interfaz de usuario o consumir un tiempo excesivo de CPU.</span><span class="sxs-lookup"><span data-stu-id="313cb-244">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="313cb-245">En lugar de usar eventos `@onmousemove` o `@onscroll` nativos, es posible que prefiera usar la interoperabilidad de JS para registrar una devolución de llamada que se active con menos frecuencia.</span><span class="sxs-lookup"><span data-stu-id="313cb-245">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="313cb-246">Por ejemplo, el siguiente componente (`MyComponent.razor`) muestra la posición del mouse, pero solo se actualiza como máximo una vez cada 500 ms:</span><span class="sxs-lookup"><span data-stu-id="313cb-246">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="313cb-247">El código de JavaScript correspondiente, que puede colocarse en la página `index.html` o cargarse como un módulo ES6, registra el cliente de escucha de eventos DOM real.</span><span class="sxs-lookup"><span data-stu-id="313cb-247">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="313cb-248">En este ejemplo, el cliente de escucha de eventos usa la [función `throttle` de Lodash](https://lodash.com/docs/4.17.15#throttle) para limitar la velocidad de las invocaciones:</span><span class="sxs-lookup"><span data-stu-id="313cb-248">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="313cb-249">Esta técnica puede ser aún más importante para Blazor Server, ya que cada invocación de eventos implica la entrega de un mensaje a través de la red.</span><span class="sxs-lookup"><span data-stu-id="313cb-249">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="313cb-250">Es útil para Blazor WebAssembly porque puede reducir en gran medida la cantidad de trabajo de representación.</span><span class="sxs-lookup"><span data-stu-id="313cb-250">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="313cb-251">Optimización de la velocidad de interoperabilidad de JavaScript</span><span class="sxs-lookup"><span data-stu-id="313cb-251">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="313cb-252">Las llamadas entre .NET y JavaScript implican una sobrecarga adicional porque:</span><span class="sxs-lookup"><span data-stu-id="313cb-252">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

* <span data-ttu-id="313cb-253">De forma predeterminada, las llamadas son asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="313cb-253">By default, calls are asynchronous.</span></span>
* <span data-ttu-id="313cb-254">De forma predeterminada, los parámetros y los valores devueltos se serializan en JSON.</span><span class="sxs-lookup"><span data-stu-id="313cb-254">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="313cb-255">Esto es para proporcionar un mecanismo de conversión fácil de comprender entre los tipos de .NET y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="313cb-255">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="313cb-256">Además, en Blazor Server, estas llamadas se pasan a través de la red.</span><span class="sxs-lookup"><span data-stu-id="313cb-256">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="313cb-257">Evitación de llamadas excesivamente detalladas</span><span class="sxs-lookup"><span data-stu-id="313cb-257">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="313cb-258">Puesto que cada llamada implica cierta sobrecarga, puede ser útil reducir el número de llamadas.</span><span class="sxs-lookup"><span data-stu-id="313cb-258">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="313cb-259">Considere el siguiente código, que almacena una colección de elementos en el almacén `localStorage` del explorador:</span><span class="sxs-lookup"><span data-stu-id="313cb-259">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="313cb-260">En el ejemplo anterior se realiza una llamada de interoperabilidad de JS independiente para cada elemento.</span><span class="sxs-lookup"><span data-stu-id="313cb-260">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="313cb-261">En su lugar, el enfoque siguiente reduce la interoperabilidad de JS a una sola llamada:</span><span class="sxs-lookup"><span data-stu-id="313cb-261">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="313cb-262">La función de JavaScript correspondiente se define de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="313cb-262">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="313cb-263">Para Blazor WebAssembly, esto normalmente solo es importante si va a realizar un gran número de llamadas de interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="313cb-263">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="313cb-264">Consideración de la posibilidad de realizar llamadas sincrónicas</span><span class="sxs-lookup"><span data-stu-id="313cb-264">Consider making synchronous calls</span></span>

<span data-ttu-id="313cb-265">De forma predeterminada, las llamadas de interoperabilidad de JavaScript son asincrónicas, independientemente de si el código al que se llama es sincrónico o asincrónico.</span><span class="sxs-lookup"><span data-stu-id="313cb-265">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="313cb-266">El motivo es asegurarse de que los componentes son compatibles con Blazor WebAssembly y Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="313cb-266">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="313cb-267">En Blazor Server, todas las llamadas de interoperabilidad de JavaScript deben ser asincrónicas porque se envían a través de una conexión de red.</span><span class="sxs-lookup"><span data-stu-id="313cb-267">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="313cb-268">Si sabe con certeza que la aplicación solo se ejecuta en Blazor WebAssembly, puede optar por realizar llamadas de interoperabilidad de JavaScript sincrónicas.</span><span class="sxs-lookup"><span data-stu-id="313cb-268">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="313cb-269">Esto tiene una sobrecarga ligeramente menor que la realización de llamadas asincrónicas y puede dar lugar a menos ciclos de representación porque no hay ningún estado intermedio mientras se esperan los resultados.</span><span class="sxs-lookup"><span data-stu-id="313cb-269">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="313cb-270">Para hacer una llamada sincrónica de .NET a JavaScript, convierta <xref:Microsoft.JSInterop.IJSRuntime> en <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span><span class="sxs-lookup"><span data-stu-id="313cb-270">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="313cb-271">Al trabajar con `IJSObjectReference`, puede realizar una llamada sincrónica convirtiéndola en `IJSInProcessObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="313cb-271">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="313cb-272">Para hacer una llamada sincrónica desde JavaScript a .NET, use `DotNet.invokeMethod` en lugar de `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="313cb-272">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="313cb-273">Las llamadas sincrónicas funcionan si:</span><span class="sxs-lookup"><span data-stu-id="313cb-273">Synchronous calls work if:</span></span>

* <span data-ttu-id="313cb-274">La aplicación se ejecuta en Blazor WebAssembly, no en Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="313cb-274">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="313cb-275">La función a la que se ha llamado devuelve un valor sincrónicamente (no es un método `async` y no devuelve un valor <xref:System.Threading.Tasks.Task>de .NET o `Promise` de JavaScript).</span><span class="sxs-lookup"><span data-stu-id="313cb-275">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="313cb-276">Para más información, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="313cb-276">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="313cb-277">Consideración de la posibilidad de realizar llamadas deserializadas</span><span class="sxs-lookup"><span data-stu-id="313cb-277">Consider making unmarshalled calls</span></span>

<span data-ttu-id="313cb-278">Cuando se ejecuta en Blazor WebAssembly, es posible realizar llamadas deserializadas de .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="313cb-278">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="313cb-279">Se trata de llamadas sincrónicas que no realizan la serialización de JSON de argumentos o valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="313cb-279">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="313cb-280">Todos los aspectos de la administración de la memoria y las traslaciones entre las representaciones de .NET y JavaScript quedan en manos del desarrollador.</span><span class="sxs-lookup"><span data-stu-id="313cb-280">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="313cb-281">Aunque el uso de `IJSUnmarshalledRuntime` es el método de interoperabilidad de JavaScript con menor sobrecarga, las API de JavaScript necesarias para interactuar con estas API no están documentadas en este momento y están sujetas a cambios importantes en versiones futuras.</span><span class="sxs-lookup"><span data-stu-id="313cb-281">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="313cb-282">Minimización del tamaño de descarga de la aplicación</span><span class="sxs-lookup"><span data-stu-id="313cb-282">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="313cb-283">Recorte de lenguaje intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="313cb-283">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="313cb-284">[Cuando una aplicación Blazor WebAssembly se recorta](xref:blazor/host-and-deploy/configure-trimmer), el tamaño de la aplicación se reduce quitando el código que no se usa en los archivos binarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="313cb-284">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="313cb-285">De forma predeterminada, el recortador se ejecuta al publicar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="313cb-285">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="313cb-286">Para sacar partido del recorte, publique la aplicación de implementación mediante el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:</span><span class="sxs-lookup"><span data-stu-id="313cb-286">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="313cb-287">Vinculación de lenguaje intermedio</span><span class="sxs-lookup"><span data-stu-id="313cb-287">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="313cb-288">[Cuando una aplicación Blazor WebAssembly se vincula](xref:blazor/host-and-deploy/configure-linker), el tamaño de la aplicación se reduce recortando el código que no se usa en los archivos binarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="313cb-288">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="313cb-289">De forma predeterminada, el enlazador de lenguaje intermedio (IL) solo está habilitado cuando se compila en la configuración de `Release`.</span><span class="sxs-lookup"><span data-stu-id="313cb-289">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="313cb-290">Para sacar partido de esto, publique la aplicación de implementación con el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:</span><span class="sxs-lookup"><span data-stu-id="313cb-290">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="313cb-291">Uso de System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="313cb-291">Use System.Text.Json</span></span>

<span data-ttu-id="313cb-292">La implementación de interoperabilidad de JavaScript de Blazor se basa en <xref:System.Text.Json>, que es una biblioteca de serialización de JSON de alto rendimiento con una asignación de memoria reducida.</span><span class="sxs-lookup"><span data-stu-id="313cb-292">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="313cb-293">El uso de <xref:System.Text.Json> no da como resultado un mayor tamaño de carga de aplicación frente a la adición de una o varias bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="313cb-293">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="313cb-294">Para obtener instrucciones sobre la migración, vea [Procedimiento para realizar la migración de `Newtonsoft.Json` a `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="313cb-294">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="313cb-295">Ensamblados de carga diferida</span><span class="sxs-lookup"><span data-stu-id="313cb-295">Lazy load assemblies</span></span>

<span data-ttu-id="313cb-296">Cargue los ensamblados en tiempo de ejecución cuando una ruta los requiera.</span><span class="sxs-lookup"><span data-stu-id="313cb-296">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="313cb-297">Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="313cb-297">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="313cb-298">Compresión</span><span class="sxs-lookup"><span data-stu-id="313cb-298">Compression</span></span>

<span data-ttu-id="313cb-299">Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="313cb-299">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="313cb-300">Blazor se basa en el servidor para realizar negociación de contenido y proporcionar archivos comprimidos estáticamente.</span><span class="sxs-lookup"><span data-stu-id="313cb-300">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="313cb-301">Cuando una aplicación se haya implementado, compruebe que proporciona archivos comprimidos.</span><span class="sxs-lookup"><span data-stu-id="313cb-301">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="313cb-302">Inspeccione la pestaña Red de las herramientas de desarrollo del explorador y compruebe que los archivos se proporcionan con `Content-Encoding: br` o `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="313cb-302">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="313cb-303">Si el host no proporciona archivos comprimidos, siga las instrucciones de <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="313cb-303">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="313cb-304">Deshabilitar las características sin usar</span><span class="sxs-lookup"><span data-stu-id="313cb-304">Disable unused features</span></span>

<span data-ttu-id="313cb-305">El runtime de Blazor WebAssembly incluye las siguientes características de .NET, que se pueden deshabilitar si la aplicación no las necesita cuando el tamaño de la carga útil es más pequeño:</span><span class="sxs-lookup"><span data-stu-id="313cb-305">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="313cb-306">Se incluye un archivo de datos para que la información de zona horaria sea correcta.</span><span class="sxs-lookup"><span data-stu-id="313cb-306">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="313cb-307">Si la aplicación no necesita esta característica, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorEnableTimeZoneSupport` del archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="313cb-307">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="313cb-308">De forma predeterminada, Blazor WebAssembly incluye los recursos de globalización necesarios para mostrar valores, como las fechas y la moneda, en la referencia cultural del usuario.</span><span class="sxs-lookup"><span data-stu-id="313cb-308">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="313cb-309">Si la aplicación no requiere localización, es posible [configurar la aplicación para que admita la referencia cultural invariable](xref:blazor/globalization-localization), que se basa en la referencia cultural `en-US`:</span><span class="sxs-lookup"><span data-stu-id="313cb-309">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="313cb-310">Se incluye información de intercalación para que API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="313cb-310">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="313cb-311">Si está convencido de que la aplicación no necesita datos de intercalación, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorWebAssemblyPreserveCollationData` del archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="313cb-311">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
