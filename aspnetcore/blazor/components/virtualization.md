---
title: Virtualización de componentes de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo usar la virtualización de componentes en aplicaciones de ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393813"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="76ac6-103">Virtualización de componentes de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="76ac6-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="76ac6-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="76ac6-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="76ac6-105">Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="76ac6-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="76ac6-106">La virtualización es una técnica para limitar la representación de la interfaz de usuario a únicamente las partes visibles actualmente.</span><span class="sxs-lookup"><span data-stu-id="76ac6-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="76ac6-107">Por ejemplo, la virtualización es útil cuando la aplicación debe representar una lista larga de elementos y solo es necesario que haya un subconjunto de elementos visible en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="76ac6-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="76ac6-108">Blazor proporciona el componente `Virtualize`, que se puede usar para agregar virtualización a los componentes de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="76ac6-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="76ac6-109">Sin la virtualización, una lista típica podría usar un bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) de C# para representar cada elemento de la lista:</span><span class="sxs-lookup"><span data-stu-id="76ac6-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="76ac6-110">Si la lista contiene miles de elementos, la representación de la lista podría tardar mucho tiempo,</span><span class="sxs-lookup"><span data-stu-id="76ac6-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="76ac6-111">con el consiguiente retraso de la interfaz de usuario que puede experimentarse.</span><span class="sxs-lookup"><span data-stu-id="76ac6-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="76ac6-112">En lugar de representar todos y cada uno de los elementos de la lista a la vez, reemplace el bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) por el componente `Virtualize` y especifique un origen de elemento fijo con `Items`.</span><span class="sxs-lookup"><span data-stu-id="76ac6-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="76ac6-113">Solo se representarán los elementos visibles actualmente:</span><span class="sxs-lookup"><span data-stu-id="76ac6-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="76ac6-114">Si no especifica un contexto en el componente con `Context`, use el valor de `context` (`@context.{PROPERTY}`) en la plantilla de contenido del elemento:</span><span class="sxs-lookup"><span data-stu-id="76ac6-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="76ac6-115">El componente `Virtualize` calcula el número de elementos que se van a representar en función del alto del contenedor y del tamaño de los elementos representados.</span><span class="sxs-lookup"><span data-stu-id="76ac6-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="76ac6-116">El contenido del elemento para el componente `Virtualize` puede incluir lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="76ac6-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="76ac6-117">HTML sin formato y código Razor, tal como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="76ac6-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="76ac6-118">Uno o más componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="76ac6-118">One or more Razor components.</span></span>
* <span data-ttu-id="76ac6-119">Una combinación de componentes HTML/Razor y Razor.</span><span class="sxs-lookup"><span data-stu-id="76ac6-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="76ac6-120">Delegado de proveedor de elementos</span><span class="sxs-lookup"><span data-stu-id="76ac6-120">Item provider delegate</span></span>

<span data-ttu-id="76ac6-121">Si no se quieren cargar todos los elementos en la memoria, se puede especificar un método de delegado de proveedor de elementos en el parámetro `ItemsProvider` del componente, que recupera los elementos solicitados a petición de manera asincrónica:</span><span class="sxs-lookup"><span data-stu-id="76ac6-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="76ac6-122">El proveedor de elementos recibe un objeto `ItemsProviderRequest`, que especifica el número necesario de elementos empezando por un índice de inicio específico.</span><span class="sxs-lookup"><span data-stu-id="76ac6-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="76ac6-123">Luego, el proveedor de elementos recupera los elementos solicitados de una base de datos u otro servicio y los devuelve como un objeto `ItemsProviderResult<TItem>` junto con un recuento total de elementos.</span><span class="sxs-lookup"><span data-stu-id="76ac6-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="76ac6-124">El proveedor de elementos puede elegir entre recuperar los elementos con cada solicitud o almacenarlos en la memoria caché para que estén disponibles fácilmente.</span><span class="sxs-lookup"><span data-stu-id="76ac6-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="76ac6-125">No intente usar un proveedor de elementos y asignar una colección a `Items` con el mismo componente `Virtualize`.</span><span class="sxs-lookup"><span data-stu-id="76ac6-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="76ac6-126">En el siguiente ejemplo se cargan empleados desde `EmployeeService`:</span><span class="sxs-lookup"><span data-stu-id="76ac6-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="76ac6-127">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="76ac6-127">Placeholder</span></span>

<span data-ttu-id="76ac6-128">Dado que la solicitud de elementos de un origen de datos remoto puede tardar tiempo, existe la posibilidad de representar un marcador de posición (`<Placeholder>...</Placeholder>`) hasta que los datos del elemento estén disponibles:</span><span class="sxs-lookup"><span data-stu-id="76ac6-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="76ac6-129">Tamaño del elemento</span><span class="sxs-lookup"><span data-stu-id="76ac6-129">Item size</span></span>

<span data-ttu-id="76ac6-130">El tamaño de cada elemento en píxeles se puede establecer con `ItemSize` (valor predeterminado: 50 px):</span><span class="sxs-lookup"><span data-stu-id="76ac6-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="76ac6-131">Recuento de sobrebarridos</span><span class="sxs-lookup"><span data-stu-id="76ac6-131">Overscan count</span></span>

<span data-ttu-id="76ac6-132">`OverscanCount` determina el número de elementos adicionales que se representan antes y después del área visible.</span><span class="sxs-lookup"><span data-stu-id="76ac6-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="76ac6-133">Esta configuración ayuda a reducir la frecuencia de representación durante el desplazamiento.</span><span class="sxs-lookup"><span data-stu-id="76ac6-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="76ac6-134">Pese a esto, unos valores superiores hacen que se representen más elementos en la página (valor predeterminado: 3):</span><span class="sxs-lookup"><span data-stu-id="76ac6-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="76ac6-135">Por ejemplo, una cuadrícula o una lista que representa cientos de filas que contienen componentes hace un uso intensivo del procesador para lograr la representación.</span><span class="sxs-lookup"><span data-stu-id="76ac6-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="76ac6-136">Así pues, considere la posibilidad de virtualizar un diseño de lista o cuadrícula para que solo se represente un subconjunto de los componentes en un momento determinado.</span><span class="sxs-lookup"><span data-stu-id="76ac6-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="76ac6-137">Para obtener un ejemplo de representación de un subconjunto de componentes, vea los siguientes componentes en la [aplicación de ejemplo de `Virtualization` (repositorio de GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="76ac6-137">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="76ac6-138">Componente `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente escrito en C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para representar un conjunto de filas de datos meteorológicos en función el desplazamiento del usuario con el ratón.</span><span class="sxs-lookup"><span data-stu-id="76ac6-138">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="76ac6-139">Componente `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa el componente `Virtualize` para mostrar 25 filas de datos meteorológicos a la vez.</span><span class="sxs-lookup"><span data-stu-id="76ac6-139">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="76ac6-140">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="76ac6-140">State changes</span></span>

<span data-ttu-id="76ac6-141">Al realizar cambios en los elementos representados por el componente `Virtualize`, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forzar que el componente vuelva a evaluarse y representarse.</span><span class="sxs-lookup"><span data-stu-id="76ac6-141">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
