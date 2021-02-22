---
title: Virtualización de componentes de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo usar la virtualización de componentes en aplicaciones de ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280022"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="c6c82-103">Virtualización de componentes de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c6c82-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="c6c82-104">Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="c6c82-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="c6c82-105">La virtualización es una técnica para limitar la representación de la interfaz de usuario a únicamente las partes visibles actualmente.</span><span class="sxs-lookup"><span data-stu-id="c6c82-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="c6c82-106">Por ejemplo, la virtualización es útil cuando la aplicación debe representar una lista larga de elementos y solo es necesario que haya un subconjunto de elementos visible en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="c6c82-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="c6c82-107">Blazor proporciona el [componente `Virtualize`](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601), que se puede usar para agregar virtualización a los componentes de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c6c82-107">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="c6c82-108">El componente `Virtualize` se puede usar cuando:</span><span class="sxs-lookup"><span data-stu-id="c6c82-108">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="c6c82-109">La representación de un conjunto de elementos de datos en un bucle.</span><span class="sxs-lookup"><span data-stu-id="c6c82-109">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="c6c82-110">La mayoría de los elementos no están visibles debido al desplazamiento.</span><span class="sxs-lookup"><span data-stu-id="c6c82-110">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="c6c82-111">Los elementos representados tienen exactamente el mismo tamaño.</span><span class="sxs-lookup"><span data-stu-id="c6c82-111">The rendered items are exactly the same size.</span></span> <span data-ttu-id="c6c82-112">Cuando el usuario se desplaza a un punto arbitrario, el componente puede calcular los elementos visibles que se van a mostrar.</span><span class="sxs-lookup"><span data-stu-id="c6c82-112">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="c6c82-113">Sin la virtualización, una lista típica podría usar un bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) de C# para representar cada elemento de la lista:</span><span class="sxs-lookup"><span data-stu-id="c6c82-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="c6c82-114">Si la lista contiene miles de elementos, la representación de la lista podría tardar mucho tiempo,</span><span class="sxs-lookup"><span data-stu-id="c6c82-114">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="c6c82-115">con el consiguiente retraso de la interfaz de usuario que puede experimentarse.</span><span class="sxs-lookup"><span data-stu-id="c6c82-115">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="c6c82-116">En lugar de representar todos y cada uno de los elementos de la lista a la vez, reemplace el bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) por el componente `Virtualize` y especifique un origen de elemento fijo con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c6c82-116">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c6c82-117">Solo se representarán los elementos visibles actualmente:</span><span class="sxs-lookup"><span data-stu-id="c6c82-117">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="c6c82-118">Si no especifica un contexto para el componente con `Context`, use el valor `context` en la plantilla de contenido del elemento:</span><span class="sxs-lookup"><span data-stu-id="c6c82-118">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="c6c82-119">El proceso de asignación de objetos de modelo a elementos y componentes se puede controlar con el atributo de la directiva [`@key`](xref:mvc/views/razor#key).</span><span class="sxs-lookup"><span data-stu-id="c6c82-119">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="c6c82-120">`@key` hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave.</span><span class="sxs-lookup"><span data-stu-id="c6c82-120">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="c6c82-121">Para más información, consulte los siguientes artículos.</span><span class="sxs-lookup"><span data-stu-id="c6c82-121">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="c6c82-122">Referencia sobre la sintaxis de Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6c82-122">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="c6c82-123">El componente `Virtualize`:</span><span class="sxs-lookup"><span data-stu-id="c6c82-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="c6c82-124">calcula el número de elementos que se van a representar en función del alto del contenedor y del tamaño de los elementos representados.</span><span class="sxs-lookup"><span data-stu-id="c6c82-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="c6c82-125">Vuelve a calcular los elementos y los representa a medida que el usuario se desplaza.</span><span class="sxs-lookup"><span data-stu-id="c6c82-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="c6c82-126">Solo captura el segmento de registros de una API externa que se corresponde a la región visible actual, en lugar de descargar todos los datos de la colección.</span><span class="sxs-lookup"><span data-stu-id="c6c82-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="c6c82-127">El contenido del elemento para el componente `Virtualize` puede incluir lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c6c82-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="c6c82-128">HTML sin formato y código Razor, tal como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="c6c82-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="c6c82-129">Uno o más componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="c6c82-129">One or more Razor components.</span></span>
* <span data-ttu-id="c6c82-130">Una combinación de componentes HTML/Razor y Razor.</span><span class="sxs-lookup"><span data-stu-id="c6c82-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="c6c82-131">Delegado de proveedor de elementos</span><span class="sxs-lookup"><span data-stu-id="c6c82-131">Item provider delegate</span></span>

<span data-ttu-id="c6c82-132">Si no se quieren cargar todos los elementos en la memoria, se puede especificar un método de delegado de proveedor de elementos en el parámetro <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> del componente, que recupera de manera asincrónica los elementos solicitados a petición.</span><span class="sxs-lookup"><span data-stu-id="c6c82-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="c6c82-133">En el ejemplo siguiente, el método `LoadEmployees` proporciona los elementos al componente `Virtualize`:</span><span class="sxs-lookup"><span data-stu-id="c6c82-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="c6c82-134">El proveedor de elementos recibe un objeto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, que especifica el número necesario de elementos empezando por un índice de inicio específico.</span><span class="sxs-lookup"><span data-stu-id="c6c82-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="c6c82-135">Luego, el proveedor de elementos recupera los elementos solicitados de una base de datos u otro servicio y los devuelve como un objeto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> junto con un recuento total de elementos.</span><span class="sxs-lookup"><span data-stu-id="c6c82-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="c6c82-136">El proveedor de elementos puede elegir entre recuperar los elementos con cada solicitud o almacenarlos en la memoria caché para que estén disponibles fácilmente.</span><span class="sxs-lookup"><span data-stu-id="c6c82-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="c6c82-137">Un componente `Virtualize` solo puede aceptar **un origen de elementos** de sus parámetros, por lo que no intente usar simultáneamente un proveedor de elementos y asignar una colección a `Items`.</span><span class="sxs-lookup"><span data-stu-id="c6c82-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="c6c82-138">Si se asignan ambos, se genera una clase <xref:System.InvalidOperationException> cuando los parámetros del componente se establecen en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c6c82-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="c6c82-139">En el ejemplo del método `LoadEmployees` siguiente se cargan empleados de `EmployeeService` (no se muestra):</span><span class="sxs-lookup"><span data-stu-id="c6c82-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="c6c82-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indica al componente que vuelva a solicitar datos de su elemento <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="c6c82-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="c6c82-141">Esto resulta útil cuando cambian los datos externos.</span><span class="sxs-lookup"><span data-stu-id="c6c82-141">This is useful when external data changes.</span></span> <span data-ttu-id="c6c82-142">No es necesario llamar a este método cuando se usa <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span><span class="sxs-lookup"><span data-stu-id="c6c82-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="c6c82-143">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="c6c82-143">Placeholder</span></span>

<span data-ttu-id="c6c82-144">Como la solicitud de elementos de un origen de datos remoto puede tardar tiempo, tiene la opción de representar un marcador de posición con contenido de los elementos:</span><span class="sxs-lookup"><span data-stu-id="c6c82-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="c6c82-145">Use una instancia de <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) para mostrar el contenido hasta que los datos del elemento estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="c6c82-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="c6c82-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> para establecer la plantilla de elemento de la lista.</span><span class="sxs-lookup"><span data-stu-id="c6c82-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="c6c82-147">Tamaño del elemento</span><span class="sxs-lookup"><span data-stu-id="c6c82-147">Item size</span></span>

<span data-ttu-id="c6c82-148">El alto de cada elemento en píxeles se puede establecer con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valor predeterminado: 50):</span><span class="sxs-lookup"><span data-stu-id="c6c82-148">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="c6c82-149">De forma predeterminada, el componente `Virtualize` mide el tamaño de representación real *después* de que se produzca la inicial.</span><span class="sxs-lookup"><span data-stu-id="c6c82-149">By default, the `Virtualize` component measures the actual rendering size *after* the initial render occurs.</span></span> <span data-ttu-id="c6c82-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> para proporcionar de antemano un tamaño de elemento exacto, con el fin de permitir un rendimiento de representación inicial preciso y garantizar la posición de desplazamiento correcta para las recargas de páginas.</span><span class="sxs-lookup"><span data-stu-id="c6c82-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="c6c82-151">Si el valor predeterminado de <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> hace que algunos elementos se representen fuera de la vista actualmente visible, se desencadena una segunda representación.</span><span class="sxs-lookup"><span data-stu-id="c6c82-151">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="c6c82-152">Para mantener correctamente la posición de desplazamiento del explorador en una lista virtualizada, la representación inicial debe ser correcta.</span><span class="sxs-lookup"><span data-stu-id="c6c82-152">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="c6c82-153">De lo contrario, los usuarios podrían ver elementos incorrectos.</span><span class="sxs-lookup"><span data-stu-id="c6c82-153">If not, users might view the wrong items.</span></span> 

## <a name="overscan-count"></a><span data-ttu-id="c6c82-154">Recuento de sobrebarridos</span><span class="sxs-lookup"><span data-stu-id="c6c82-154">Overscan count</span></span>

<span data-ttu-id="c6c82-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determina el número de elementos adicionales que se representan antes y después del área visible.</span><span class="sxs-lookup"><span data-stu-id="c6c82-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="c6c82-156">Esta configuración ayuda a reducir la frecuencia de representación durante el desplazamiento.</span><span class="sxs-lookup"><span data-stu-id="c6c82-156">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="c6c82-157">Pese a esto, unos valores superiores hacen que se representen más elementos en la página (valor predeterminado: 3):</span><span class="sxs-lookup"><span data-stu-id="c6c82-157">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="c6c82-158">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="c6c82-158">State changes</span></span>

<span data-ttu-id="c6c82-159">Al realizar cambios en los elementos representados por el componente `Virtualize`, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forzar que el componente vuelva a evaluarse y representarse.</span><span class="sxs-lookup"><span data-stu-id="c6c82-159">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="c6c82-160">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="c6c82-160">For more information, see <xref:blazor/components/rendering>.</span></span>
