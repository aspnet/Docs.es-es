---
title: Componentes con plantilla de Blazor en ASP.NET Core
author: guardrex
description: Descubra cómo los componentes con plantilla pueden aceptar una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: f818a0b7f1ba6d4dd6affeeba785c5e288568dd8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507959"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a>Componentes con plantilla de Blazor en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes con plantilla son componentes que aceptan una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente. Los componentes con plantilla permiten crear componentes de nivel superior que son más reutilizables que los componentes normales. Estos son un par de ejemplos:

* Un componente de tabla que permite a un usuario especificar plantillas para el encabezado, las filas y el pie de página de la tabla.
* Un componente de lista que permite a un usuario especificar una plantilla para representar elementos en una lista.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>Parámetros de plantilla

Un componente con plantilla se define especificando uno o más parámetros de componente de tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> o <xref:Microsoft.AspNetCore.Components.RenderFragment%601>. Un fragmento de representación representa un segmento de interfaz de usuario que se va a representar. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> toma un parámetro de tipo que se puede especificar cuando se invoca el fragmento de representación.

Componente `TableTemplate` (`TableTemplate.razor`):

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Al usar un componente con plantilla, los parámetros de plantilla se pueden especificar utilizando los elementos secundarios que coinciden con los nombres de los parámetros (`TableHeader` y `RowTemplate` en el ejemplo siguiente):

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> La restricciones de tipo genérico se admitirán en una versión futura. Para obtener más información, vea [Permitir restricciones de tipo genérico (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parámetros de contexto de la plantilla

Los argumentos de los componentes de tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> pasados como elementos tienen un parámetro implícito denominado `context` (por ejemplo, en el ejemplo de código anterior, `@context.PetId`), pero puede cambiar el nombre del parámetro mediante el atributo `Context` en el elemento secundario. En el ejemplo siguiente, el atributo `Context` del elemento `RowTemplate` especifica el parámetro `pet`:

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

También puede especificar el atributo `Context` en el elemento de componente. El atributo `Context` especificado se aplica a todos los parámetros de plantilla especificados. Esto puede ser útil si desea especificar el nombre del parámetro de contenido para el contenido secundario implícito (sin ningún elemento secundario de ajuste). En el ejemplo siguiente, el atributo `Context` aparece en el elemento `TableTemplate` y se aplica a todos los parámetros de plantilla:

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a>Componentes de tipo genérico

Los componentes con plantilla suelen tener tipos genéricos. Por ejemplo, se puede usar un componente `ListViewTemplate` genérico (`ListViewTemplate.razor`) para representar valores `IEnumerable<T>`. Para definir un componente genérico, utilice la directiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parámetros de tipo:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Al usar componentes de tipo genérico, el parámetro de tipo se infiere si es posible:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

De lo contrario, el parámetro de tipo se debe especificar explícitamente mediante un atributo que coincida con el nombre del parámetro de tipo. En el ejemplo siguiente, `TItem="Pet"` especifica el tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
