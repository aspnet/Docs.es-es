---
title: Aplicación auxiliar de etiquetas de componentes en ASP.NET Core
author: guardrex
ms.author: riande
description: 'Aprenda a usar la aplicación auxiliar de etiquetas de componentes de ASP.NET Core para representar Razor componentes en páginas y vistas.'
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431048"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="cc134-103">Aplicación auxiliar de etiquetas de componentes en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc134-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="cc134-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cc134-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc134-105">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="cc134-105">Prerequisites</span></span>

<span data-ttu-id="cc134-106">Siga las instrucciones de la sección de *configuración* para:</span><span class="sxs-lookup"><span data-stu-id="cc134-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="cc134-107">Aplicación auxiliar de etiquetas de componentes</span><span class="sxs-lookup"><span data-stu-id="cc134-107">Component Tag Helper</span></span>

<span data-ttu-id="cc134-108">Para representar un componente de una página o vista, use la [aplicación auxiliar de etiquetas de componentes](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` etiqueta).</span><span class="sxs-lookup"><span data-stu-id="cc134-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="cc134-109">La integración de Razor componentes en Razor páginas y aplicaciones MVC en una *Blazor WebAssembly aplicación hospedada* se admite en ASP.net Core en .net 5,0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="cc134-109">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="cc134-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="cc134-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="cc134-111">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="cc134-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="cc134-112">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="cc134-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cc134-113">Blazor WebAssembly los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="cc134-113">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="cc134-114">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="cc134-114">Render Mode</span></span> | <span data-ttu-id="cc134-115">Descripción</span><span class="sxs-lookup"><span data-stu-id="cc134-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="cc134-116">Representa un marcador para una Blazor WebAssembly aplicación que se usa para incluir un componente interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="cc134-116">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="cc134-117">El componente no está prerepresentado.</span><span class="sxs-lookup"><span data-stu-id="cc134-117">The component isn't prerendered.</span></span> <span data-ttu-id="cc134-118">Esta opción facilita la representación Blazor WebAssembly de diferentes componentes en diferentes páginas.</span><span class="sxs-lookup"><span data-stu-id="cc134-118">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="cc134-119">Representa el componente en HTML estático e incluye un marcador para una Blazor WebAssembly aplicación para su uso posterior con el fin de que el componente sea interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="cc134-119">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="cc134-120">Blazor Server los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="cc134-120">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="cc134-121">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="cc134-121">Render Mode</span></span> | <span data-ttu-id="cc134-122">Descripción</span><span class="sxs-lookup"><span data-stu-id="cc134-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc134-123">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cc134-123">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc134-124">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc134-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc134-125">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cc134-125">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc134-126">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="cc134-126">Output from the component isn't included.</span></span> <span data-ttu-id="cc134-127">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc134-127">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc134-128">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="cc134-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cc134-129">Blazor Server los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="cc134-129">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="cc134-130">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="cc134-130">Render Mode</span></span> | <span data-ttu-id="cc134-131">Descripción</span><span class="sxs-lookup"><span data-stu-id="cc134-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc134-132">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cc134-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc134-133">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc134-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc134-134">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="cc134-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc134-135">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="cc134-135">Output from the component isn't included.</span></span> <span data-ttu-id="cc134-136">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="cc134-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc134-137">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="cc134-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="cc134-138">Entre las características adicionales se incluyen:</span><span class="sxs-lookup"><span data-stu-id="cc134-138">Additional characteristics include:</span></span>

* <span data-ttu-id="cc134-139">Se permiten varias aplicaciones auxiliares de etiquetas de componentes que representan varios Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="cc134-139">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="cc134-140">Los componentes no se pueden representar dinámicamente una vez iniciada la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc134-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="cc134-141">Mientras que las páginas y las vistas pueden utilizar componentes, el opuesto no es cierto.</span><span class="sxs-lookup"><span data-stu-id="cc134-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="cc134-142">Los componentes no pueden usar características específicas de la página y de la vista, como vistas y secciones parciales.</span><span class="sxs-lookup"><span data-stu-id="cc134-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="cc134-143">Para usar la lógica de una vista parcial en un componente, se debe factorizar la lógica de vista parcial en un componente.</span><span class="sxs-lookup"><span data-stu-id="cc134-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="cc134-144">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="cc134-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="cc134-145">La siguiente aplicación auxiliar de etiquetas de componentes representa el `Counter` componente en una página o una vista de una Blazor Server aplicación con `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="cc134-145">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="cc134-146">En el ejemplo anterior se supone que el `Counter` componente está en la carpeta *páginas* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc134-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="cc134-147">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Pages` o `@using BlazorSample.Client.Pages` en una solución hospedada Blazor ).</span><span class="sxs-lookup"><span data-stu-id="cc134-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="cc134-148">La aplicación auxiliar de etiquetas de componente también puede pasar parámetros a los componentes.</span><span class="sxs-lookup"><span data-stu-id="cc134-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="cc134-149">Considere el siguiente `ColorfulCheckbox` componente que establece el color y el tamaño de la etiqueta de casilla:</span><span class="sxs-lookup"><span data-stu-id="cc134-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="cc134-150">Los `Size` `int` parámetros de componente () y `Color` ( `string` ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componentes: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="cc134-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="cc134-151">En el ejemplo anterior se supone que el `ColorfulCheckbox` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc134-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="cc134-152">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="cc134-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="cc134-153">El siguiente código HTML se representa en la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="cc134-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="cc134-154">El paso de una cadena entrecomillada requiere una [ Razor expresión explícita](xref:mvc/views/razor#explicit-razor-expressions), como se muestra `param-Color` en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="cc134-154">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="cc134-155">El Razor comportamiento de análisis de un `string` valor de tipo no se aplica a un `param-*` atributo porque el atributo es un `object` tipo.</span><span class="sxs-lookup"><span data-stu-id="cc134-155">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="cc134-156">Se admiten todos los tipos de parámetros, excepto:</span><span class="sxs-lookup"><span data-stu-id="cc134-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="cc134-157">Parámetros genéricos.</span><span class="sxs-lookup"><span data-stu-id="cc134-157">Generic parameters.</span></span>
* <span data-ttu-id="cc134-158">Parámetros no serializables.</span><span class="sxs-lookup"><span data-stu-id="cc134-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="cc134-159">Herencia en parámetros de colección.</span><span class="sxs-lookup"><span data-stu-id="cc134-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="cc134-160">Los parámetros cuyo tipo se define fuera de la Blazor WebAssembly aplicación o dentro de un ensamblado cargado de forma diferida.</span><span class="sxs-lookup"><span data-stu-id="cc134-160">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="cc134-161">El tipo de parámetro debe ser serializable de JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y las propiedades configurables.</span><span class="sxs-lookup"><span data-stu-id="cc134-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="cc134-162">Por ejemplo, puede especificar un valor para `Size` y `Color` en el ejemplo anterior porque los tipos de `Size` y `Color` son tipos primitivos ( `int` y `string` ), que son compatibles con el serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="cc134-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="cc134-163">En el ejemplo siguiente, se pasa un objeto de clase al componente:</span><span class="sxs-lookup"><span data-stu-id="cc134-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="cc134-164">*MyClass.CS* :</span><span class="sxs-lookup"><span data-stu-id="cc134-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="cc134-165">**La clase debe tener un constructor sin parámetros público.**</span><span class="sxs-lookup"><span data-stu-id="cc134-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="cc134-166">*Shared/Component. Razor* :</span><span class="sxs-lookup"><span data-stu-id="cc134-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="cc134-167">*Pages/mi. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cc134-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="cc134-168">En el ejemplo anterior se supone que el `MyComponent` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc134-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="cc134-169">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample` y `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="cc134-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="cc134-170">`MyClass` está en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="cc134-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc134-171">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cc134-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
