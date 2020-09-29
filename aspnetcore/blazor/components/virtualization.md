---
title: Virtualización de componentes de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo usar la virtualización de componentes en aplicaciones de ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847577"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>Virtualización de componentes de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27)

Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor. La virtualización es una técnica para limitar la representación de la interfaz de usuario a únicamente las partes visibles actualmente. Por ejemplo, la virtualización es útil cuando la aplicación debe representar una lista larga o una tabla con muchas filas, y solo es necesario que haya un subconjunto de elementos visible en un momento dado. Blazor proporciona el componente `Virtualize`, que se puede usar para agregar virtualización a los componentes de una aplicación.

::: moniker range=">= aspnetcore-5.0"

Sin la virtualización, el típico componente basado en una tabla o en una lista podría usar un bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) de C# para representar cada elemento de la lista o de cada fila de la tabla:

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

Si la lista contiene miles de elementos, la representación de la lista podría tardar mucho tiempo, con el consiguiente retraso de la interfaz de usuario que puede experimentarse.

En lugar de representar todos y cada uno de los elementos de la lista a la vez, reemplace el bucle [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) por el componente `Virtualize` y especifique un origen de elemento fijo con `Items`. Solo se representarán los elementos visibles actualmente:

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Si no especifica un contexto en el componente con `Context`, use el valor de `context` (`@context.{PROPERTY}`) en la plantilla de contenido del elemento:

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

El componente `Virtualize` calcula el número de elementos que se van a representar en función del alto del contenedor y del tamaño de los elementos representados.

## <a name="item-provider-delegate"></a>Delegado de proveedor de elementos

Si no se quieren cargar todos los elementos en la memoria, se puede especificar un método de delegado de proveedor de elementos en el parámetro `ItemsProvider` del componente, que recupera los elementos solicitados a petición de manera asincrónica:

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

El proveedor de elementos recibe un objeto `ItemsProviderRequest`, que especifica el número necesario de elementos empezando por un índice de inicio específico. Luego, el proveedor de elementos recupera los elementos solicitados de una base de datos u otro servicio y los devuelve como un objeto `ItemsProviderResult<TItem>` junto con un recuento total de elementos. El proveedor de elementos puede elegir entre recuperar los elementos con cada solicitud o almacenarlos en la memoria caché para que estén disponibles fácilmente. No intente usar un proveedor de elementos y asignar una colección a `Items` con el mismo componente `Virtualize`.

En el siguiente ejemplo se cargan empleados desde `EmployeeService`:

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

## <a name="placeholder"></a>Marcador de posición

Dado que la solicitud de elementos de un origen de datos remoto puede tardar tiempo, existe la posibilidad de representar un marcador de posición (`<Placeholder>...</Placeholder>`) hasta que los datos del elemento estén disponibles:

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a>Tamaño del elemento

El tamaño de cada elemento en píxeles se puede establecer con `ItemSize` (valor predeterminado: 50 px):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Recuento de sobrebarridos

`OverscanCount` determina el número de elementos adicionales que se representan antes y después del área visible. Esta configuración ayuda a reducir la frecuencia de representación durante el desplazamiento. Pese a esto, unos valores superiores hacen que se representen más elementos en la página (valor predeterminado: 3):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Por ejemplo, una cuadrícula o una lista que representa cientos de filas que contienen componentes hace un uso intensivo del procesador para lograr la representación. Así pues, considere la posibilidad de virtualizar un diseño de lista o cuadrícula para que solo se represente un subconjunto de los componentes en un momento determinado. Para obtener un ejemplo de representación de un subconjunto de componentes, vea los siguientes componentes en la [aplicación de ejemplo de `Virtualization` (repositorio de GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* Componente `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente escrito en C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para representar un conjunto de filas de datos meteorológicos en función el desplazamiento del usuario con el ratón.
* Componente `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa el componente `Virtualize` para mostrar 25 filas de datos meteorológicos a la vez.

::: moniker-end

## <a name="state-changes"></a>Cambios de estado

Al realizar cambios en los elementos representados por el componente `Virtualize`, llame a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forzar que el componente vuelva a evaluarse y representarse.
