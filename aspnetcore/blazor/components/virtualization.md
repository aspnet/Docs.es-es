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
ms.openlocfilehash: afd2da19641b41871f06426934c39348daa54b1f
ms.sourcegitcommit: 2fea9bfe6127bbbdbb438406c82529b2bc331944
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065537"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>Virtualización de componentes de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27)

Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor. La virtualización es una técnica para limitar la representación de la interfaz de usuario a únicamente las partes visibles actualmente. Por ejemplo, la virtualización es útil cuando la aplicación debe representar una lista larga de elementos y solo es necesario que haya un subconjunto de elementos visible en un momento dado. Blazor proporciona el [componente `Virtualize`](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601), que se puede usar para agregar virtualización a los componentes de una aplicación.

El componente `Virtualize` se puede usar cuando:

* La representación de un conjunto de elementos de datos en un bucle.
* La mayoría de los elementos no están visibles debido al desplazamiento.
* Los elementos representados tienen exactamente el mismo tamaño. Cuando el usuario se desplaza a un punto arbitrario, el componente puede calcular los elementos visibles que se van a mostrar.

Sin la virtualización, una lista típica podría usar un bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) de C# para representar cada elemento de la lista:

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Si la lista contiene miles de elementos, la representación de la lista podría tardar mucho tiempo, con el consiguiente retraso de la interfaz de usuario que puede experimentarse.

En lugar de representar todos y cada uno de los elementos de la lista a la vez, reemplace el bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) por el componente `Virtualize` y especifique un origen de elemento fijo con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>. Solo se representarán los elementos visibles actualmente:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Si no especifica un contexto para el componente con `Context`, use el valor `context` en la plantilla de contenido del elemento:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> El proceso de asignación de objetos de modelo a elementos y componentes se puede controlar con el atributo de la directiva [`@key`](xref:mvc/views/razor#key). `@key` hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave.
>
> Para más información, consulte los siguientes artículos.
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * <xref:mvc/views/razor#key>

El componente `Virtualize`:

* calcula el número de elementos que se van a representar en función del alto del contenedor y del tamaño de los elementos representados.
* Vuelve a calcular los elementos y los representa a medida que el usuario se desplaza.
* Solo captura el segmento de registros de una API externa que se corresponde a la región visible actual, en lugar de descargar todos los datos de la colección.

El contenido del elemento para el componente `Virtualize` puede incluir lo siguiente:

* HTML sin formato y código Razor, tal como se muestra en el ejemplo anterior.
* Uno o más componentes Razor.
* Una combinación de componentes HTML/Razor y Razor.

## <a name="item-provider-delegate"></a>Delegado de proveedor de elementos

Si no se quieren cargar todos los elementos en la memoria, se puede especificar un método de delegado de proveedor de elementos en el parámetro <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> del componente, que recupera de manera asincrónica los elementos solicitados a petición. En el ejemplo siguiente, el método `LoadEmployees` proporciona los elementos al componente `Virtualize`:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

El proveedor de elementos recibe un objeto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, que especifica el número necesario de elementos empezando por un índice de inicio específico. Luego, el proveedor de elementos recupera los elementos solicitados de una base de datos u otro servicio y los devuelve como un objeto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> junto con un recuento total de elementos. El proveedor de elementos puede elegir entre recuperar los elementos con cada solicitud o almacenarlos en la memoria caché para que estén disponibles fácilmente.

Un componente `Virtualize` solo puede aceptar **un origen de elementos** de sus parámetros, por lo que no intente usar simultáneamente un proveedor de elementos y asignar una colección a `Items`. Si se asignan ambos, se genera una clase <xref:System.InvalidOperationException> cuando los parámetros del componente se establecen en tiempo de ejecución.

En el ejemplo del método `LoadEmployees` siguiente se cargan empleados de `EmployeeService` (no se muestra):

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indica al componente que vuelva a solicitar datos de su elemento <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>. Esto resulta útil cuando cambian los datos externos. No es necesario llamar a este método cuando se usa <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.

## <a name="placeholder"></a>Marcador de posición

Como la solicitud de elementos de un origen de datos remoto puede tardar tiempo, tiene la opción de representar un marcador de posición con contenido de los elementos:

* Use una instancia de <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) para mostrar el contenido hasta que los datos del elemento estén disponibles.
* Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> para establecer la plantilla de elemento de la lista.

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

## <a name="item-size"></a>Tamaño del elemento

El tamaño de cada elemento en píxeles se puede establecer con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valor predeterminado: 50):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Recuento de sobrebarridos

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determina el número de elementos adicionales que se representan antes y después del área visible. Esta configuración ayuda a reducir la frecuencia de representación durante el desplazamiento. Pese a esto, unos valores superiores hacen que se representen más elementos en la página (valor predeterminado: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Cambios de estado

Al realizar cambios en los elementos representados por el componente `Virtualize`, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forzar que el componente vuelva a evaluarse y representarse.
