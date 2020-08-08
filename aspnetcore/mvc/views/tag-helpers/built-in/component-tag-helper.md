---
title: Aplicación auxiliar de etiquetas de componentes en ASP.NET Core
author: guardrex
ms.author: riande
description: Aprenda a usar la aplicación auxiliar de etiquetas de componentes de ASP.NET Core para representar Razor componentes en páginas y vistas.
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 09291b537e35d00df6f8006aaccdf4db12acfaea
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018694"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="726df-103">Aplicación auxiliar de etiquetas de componentes en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="726df-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="726df-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="726df-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="726df-105">Para representar un componente a partir de una página o vista, use el [asistente de etiquetas de componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="726df-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="726df-106">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="726df-106">Prerequisites</span></span>

<span data-ttu-id="726df-107">Siga las instrucciones de la sección *preparación de la aplicación para usar componentes en páginas y vistas* del <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> artículo.</span><span class="sxs-lookup"><span data-stu-id="726df-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="726df-108">Aplicación auxiliar de etiquetas de componentes</span><span class="sxs-lookup"><span data-stu-id="726df-108">Component Tag Helper</span></span>

<span data-ttu-id="726df-109">La siguiente aplicación auxiliar de etiquetas de componentes representa el `Counter` componente en una página o vista:</span><span class="sxs-lookup"><span data-stu-id="726df-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="726df-110">En el ejemplo anterior se supone que el `Counter` componente está en la carpeta *páginas* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="726df-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="726df-111">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Pages`).</span><span class="sxs-lookup"><span data-stu-id="726df-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="726df-112">La aplicación auxiliar de etiquetas de componente también puede pasar parámetros a los componentes.</span><span class="sxs-lookup"><span data-stu-id="726df-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="726df-113">Considere el siguiente `ColorfulCheckbox` componente que establece el color y el tamaño de la etiqueta de casilla:</span><span class="sxs-lookup"><span data-stu-id="726df-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="726df-114">Los `Size` `int` parámetros de componente () y `Color` ( `string` ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componentes: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="726df-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="726df-115">En el ejemplo anterior se supone que el `ColorfulCheckbox` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="726df-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="726df-116">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="726df-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="726df-117">El siguiente código HTML se representa en la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="726df-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="726df-118">El paso de una cadena entrecomillada requiere una [ Razor expresión explícita](xref:mvc/views/razor#explicit-razor-expressions), como se muestra `param-Color` en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="726df-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="726df-119">El Razor comportamiento de análisis de un `string` valor de tipo no se aplica a un `param-*` atributo porque el atributo es un `object` tipo.</span><span class="sxs-lookup"><span data-stu-id="726df-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="726df-120">El tipo de parámetro debe ser serializable de JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y las propiedades configurables.</span><span class="sxs-lookup"><span data-stu-id="726df-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="726df-121">Por ejemplo, puede especificar un valor para `Size` y `Color` en el ejemplo anterior porque los tipos de `Size` y `Color` son tipos primitivos ( `int` y `string` ), que son compatibles con el serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="726df-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="726df-122">En el ejemplo siguiente, se pasa un objeto de clase al componente:</span><span class="sxs-lookup"><span data-stu-id="726df-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="726df-123">*MyClass.CS*:</span><span class="sxs-lookup"><span data-stu-id="726df-123">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="726df-124">**La clase debe tener un constructor sin parámetros público.**</span><span class="sxs-lookup"><span data-stu-id="726df-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="726df-125">*Shared/Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="726df-125">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="726df-126">*Pages/mi. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="726df-126">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="726df-127">En el ejemplo anterior se supone que el `MyComponent` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="726df-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="726df-128">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample` y `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="726df-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="726df-129">`MyClass`está en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="726df-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="726df-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="726df-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="726df-131">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="726df-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="726df-132">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="726df-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="726df-133">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="726df-133">Render Mode</span></span> | <span data-ttu-id="726df-134">Descripción</span><span class="sxs-lookup"><span data-stu-id="726df-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="726df-135">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="726df-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="726df-136">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="726df-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="726df-137">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="726df-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="726df-138">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="726df-138">Output from the component isn't included.</span></span> <span data-ttu-id="726df-139">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="726df-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="726df-140">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="726df-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="726df-141">Mientras que las páginas y las vistas pueden utilizar componentes, el opuesto no es cierto.</span><span class="sxs-lookup"><span data-stu-id="726df-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="726df-142">Los componentes no pueden usar características específicas de la página y de la vista, como vistas y secciones parciales.</span><span class="sxs-lookup"><span data-stu-id="726df-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="726df-143">Para usar la lógica de una vista parcial en un componente, se debe factorizar la lógica de vista parcial en un componente.</span><span class="sxs-lookup"><span data-stu-id="726df-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="726df-144">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="726df-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="726df-145">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="726df-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
