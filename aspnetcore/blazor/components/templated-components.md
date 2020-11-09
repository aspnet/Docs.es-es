---
title: 'Componentes con plantilla de Blazor en ASP.NET Core'
author: guardrex
description: Descubra cómo los componentes con plantilla pueden aceptar una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/templated-components
ms.openlocfilehash: ee38040a1edc8db43b8d5bf0202fd519b153ea04
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056249"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="200f0-103">Componentes con plantilla de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="200f0-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="200f0-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="200f0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="200f0-105">Los componentes con plantilla son componentes que aceptan una o varias plantillas de interfaz de usuario como parámetros, que se pueden usar como parte de la lógica de representación del componente.</span><span class="sxs-lookup"><span data-stu-id="200f0-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="200f0-106">Los componentes con plantilla permiten crear componentes de nivel superior que son más reutilizables que los componentes normales.</span><span class="sxs-lookup"><span data-stu-id="200f0-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="200f0-107">Estos son un par de ejemplos:</span><span class="sxs-lookup"><span data-stu-id="200f0-107">A couple of examples include:</span></span>

* <span data-ttu-id="200f0-108">Un componente de tabla que permite a un usuario especificar plantillas para el encabezado, las filas y el pie de página de la tabla.</span><span class="sxs-lookup"><span data-stu-id="200f0-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="200f0-109">Un componente de lista que permite a un usuario especificar una plantilla para representar elementos en una lista.</span><span class="sxs-lookup"><span data-stu-id="200f0-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="200f0-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="200f0-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="200f0-111">Parámetros de plantilla</span><span class="sxs-lookup"><span data-stu-id="200f0-111">Template parameters</span></span>

<span data-ttu-id="200f0-112">Un componente con plantilla se define especificando uno o más parámetros de componente de tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> o <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span><span class="sxs-lookup"><span data-stu-id="200f0-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="200f0-113">Un fragmento de representación representa un segmento de interfaz de usuario que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="200f0-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="200f0-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> toma un parámetro de tipo que se puede especificar cuando se invoca el fragmento de representación.</span><span class="sxs-lookup"><span data-stu-id="200f0-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="200f0-115">Componente `TableTemplate` (`TableTemplate.razor`):</span><span class="sxs-lookup"><span data-stu-id="200f0-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="200f0-116">Al usar un componente con plantilla, los parámetros de plantilla se pueden especificar utilizando los elementos secundarios que coinciden con los nombres de los parámetros (`TableHeader` y `RowTemplate` en el ejemplo siguiente):</span><span class="sxs-lookup"><span data-stu-id="200f0-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="200f0-117">La restricciones de tipo genérico se admitirán en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="200f0-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="200f0-118">Para obtener más información, vea [Permitir restricciones de tipo genérico (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="200f0-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="200f0-119">Parámetros de contexto de la plantilla</span><span class="sxs-lookup"><span data-stu-id="200f0-119">Template context parameters</span></span>

<span data-ttu-id="200f0-120">Los argumentos de los componentes de tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> pasados como elementos tienen un parámetro implícito denominado `context` (por ejemplo, en el ejemplo de código anterior, `@context.PetId`), pero puede cambiar el nombre del parámetro mediante el atributo `Context` en el elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="200f0-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="200f0-121">En el ejemplo siguiente, el atributo `Context` del elemento `RowTemplate` especifica el parámetro `pet`:</span><span class="sxs-lookup"><span data-stu-id="200f0-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="200f0-122">También puede especificar el atributo `Context` en el elemento de componente.</span><span class="sxs-lookup"><span data-stu-id="200f0-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="200f0-123">El atributo `Context` especificado se aplica a todos los parámetros de plantilla especificados.</span><span class="sxs-lookup"><span data-stu-id="200f0-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="200f0-124">Esto puede ser útil si desea especificar el nombre del parámetro de contenido para el contenido secundario implícito (sin ningún elemento secundario de ajuste).</span><span class="sxs-lookup"><span data-stu-id="200f0-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="200f0-125">En el ejemplo siguiente, el atributo `Context` aparece en el elemento `TableTemplate` y se aplica a todos los parámetros de plantilla:</span><span class="sxs-lookup"><span data-stu-id="200f0-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="200f0-126">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="200f0-126">Generic-typed components</span></span>

<span data-ttu-id="200f0-127">Los componentes con plantilla suelen tener tipos genéricos.</span><span class="sxs-lookup"><span data-stu-id="200f0-127">Templated components are often generically typed.</span></span> <span data-ttu-id="200f0-128">Por ejemplo, se puede usar un componente `ListViewTemplate` genérico (`ListViewTemplate.razor`) para representar valores `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="200f0-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="200f0-129">Para definir un componente genérico, utilice la directiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parámetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="200f0-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="200f0-130">Al usar componentes de tipo genérico, el parámetro de tipo se infiere si es posible:</span><span class="sxs-lookup"><span data-stu-id="200f0-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

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

<span data-ttu-id="200f0-131">De lo contrario, el parámetro de tipo se debe especificar explícitamente mediante un atributo que coincida con el nombre del parámetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="200f0-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="200f0-132">En el ejemplo siguiente, `TItem="Pet"` especifica el tipo:</span><span class="sxs-lookup"><span data-stu-id="200f0-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

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
