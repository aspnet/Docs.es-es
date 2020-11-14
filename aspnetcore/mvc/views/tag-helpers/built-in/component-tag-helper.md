---
title: Aplicación auxiliar de etiquetas de componentes en ASP.NET Core
author: guardrex
ms.author: riande
description: 'Aprenda a usar la aplicación auxiliar de etiquetas de componentes de ASP.NET Core para representar :::no-loc(Razor)::: componentes en páginas y vistas.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595459"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="9550e-103">Aplicación auxiliar de etiquetas de componentes en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9550e-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="9550e-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9550e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9550e-105">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9550e-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9550e-106">Siga las instrucciones de la sección de *configuración* para:</span><span class="sxs-lookup"><span data-stu-id="9550e-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9550e-107">Siga las instrucciones de la sección de *configuración* del <xref:blazor/components/prerendering-and-integration?pivots=server> artículo.</span><span class="sxs-lookup"><span data-stu-id="9550e-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="9550e-108">Aplicación auxiliar de etiquetas de componentes</span><span class="sxs-lookup"><span data-stu-id="9550e-108">Component Tag Helper</span></span>

<span data-ttu-id="9550e-109">Para representar un componente de una página o vista, use la [aplicación auxiliar de etiquetas de componentes](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` etiqueta).</span><span class="sxs-lookup"><span data-stu-id="9550e-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="9550e-110">La integración de :::no-loc(Razor)::: componentes en :::no-loc(Razor)::: páginas y aplicaciones MVC en una *:::no-loc(Blazor WebAssembly)::: aplicación hospedada* se admite en ASP.net Core en .net 5,0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="9550e-110">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="9550e-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="9550e-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="9550e-112">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="9550e-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="9550e-113">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación :::no-loc(Blazor)::: desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="9550e-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9550e-114">:::no-loc(Blazor WebAssembly)::: los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9550e-114">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="9550e-115">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="9550e-115">Render Mode</span></span> | <span data-ttu-id="9550e-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="9550e-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="9550e-117">Representa un marcador para una :::no-loc(Blazor WebAssembly)::: aplicación que se usa para incluir un componente interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9550e-117">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="9550e-118">El componente no está prerepresentado.</span><span class="sxs-lookup"><span data-stu-id="9550e-118">The component isn't prerendered.</span></span> <span data-ttu-id="9550e-119">Esta opción facilita la representación :::no-loc(Blazor WebAssembly)::: de diferentes componentes en diferentes páginas.</span><span class="sxs-lookup"><span data-stu-id="9550e-119">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="9550e-120">Representa el componente en HTML estático e incluye un marcador para una :::no-loc(Blazor WebAssembly)::: aplicación para su uso posterior con el fin de que el componente sea interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="9550e-120">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="9550e-121">:::no-loc(Blazor Server)::: los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9550e-121">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="9550e-122">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="9550e-122">Render Mode</span></span> | <span data-ttu-id="9550e-123">Descripción</span><span class="sxs-lookup"><span data-stu-id="9550e-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="9550e-124">Representa el componente en código HTML estático e incluye un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-124">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="9550e-125">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-125">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="9550e-126">Representa un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-126">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="9550e-127">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="9550e-127">Output from the component isn't included.</span></span> <span data-ttu-id="9550e-128">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-128">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="9550e-129">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="9550e-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9550e-130">:::no-loc(Blazor Server)::: los modos de representación de la aplicación se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9550e-130">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="9550e-131">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="9550e-131">Render Mode</span></span> | <span data-ttu-id="9550e-132">Descripción</span><span class="sxs-lookup"><span data-stu-id="9550e-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="9550e-133">Representa el componente en código HTML estático e incluye un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-133">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="9550e-134">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-134">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="9550e-135">Representa un marcador para una aplicación :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-135">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="9550e-136">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="9550e-136">Output from the component isn't included.</span></span> <span data-ttu-id="9550e-137">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="9550e-137">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="9550e-138">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="9550e-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="9550e-139">Entre las características adicionales se incluyen:</span><span class="sxs-lookup"><span data-stu-id="9550e-139">Additional characteristics include:</span></span>

* <span data-ttu-id="9550e-140">Se permiten varias aplicaciones auxiliares de etiquetas de componentes que representan varios :::no-loc(Razor)::: componentes.</span><span class="sxs-lookup"><span data-stu-id="9550e-140">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="9550e-141">Los componentes no se pueden representar dinámicamente una vez iniciada la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9550e-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="9550e-142">Mientras que las páginas y las vistas pueden utilizar componentes, el opuesto no es cierto.</span><span class="sxs-lookup"><span data-stu-id="9550e-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="9550e-143">Los componentes no pueden usar características específicas de la página y de la vista, como vistas y secciones parciales.</span><span class="sxs-lookup"><span data-stu-id="9550e-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="9550e-144">Para usar la lógica de una vista parcial en un componente, se debe factorizar la lógica de vista parcial en un componente.</span><span class="sxs-lookup"><span data-stu-id="9550e-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="9550e-145">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="9550e-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="9550e-146">La siguiente aplicación auxiliar de etiquetas de componentes representa el `Counter` componente en una página o una vista de una :::no-loc(Blazor Server)::: aplicación con `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="9550e-146">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="9550e-147">En el ejemplo anterior se supone que el `Counter` componente está en la carpeta *páginas* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9550e-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="9550e-148">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using :::no-loc(Blazor):::Sample.Pages` o `@using :::no-loc(Blazor):::Sample.Client.Pages` en una solución hospedada :::no-loc(Blazor)::: ).</span><span class="sxs-lookup"><span data-stu-id="9550e-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="9550e-149">La aplicación auxiliar de etiquetas de componente también puede pasar parámetros a los componentes.</span><span class="sxs-lookup"><span data-stu-id="9550e-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="9550e-150">Considere el siguiente `ColorfulCheckbox` componente que establece el color y el tamaño de la etiqueta de casilla:</span><span class="sxs-lookup"><span data-stu-id="9550e-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="9550e-151">Los `Size` `int` parámetros de componente () y `Color` ( `string` ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componentes: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="9550e-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="9550e-152">En el ejemplo anterior se supone que el `ColorfulCheckbox` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9550e-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="9550e-153">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using :::no-loc(Blazor):::Sample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="9550e-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="9550e-154">El siguiente código HTML se representa en la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="9550e-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="9550e-155">El paso de una cadena entrecomillada requiere una [ :::no-loc(Razor)::: expresión explícita](xref:mvc/views/razor#explicit-razor-expressions), como se muestra `param-Color` en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="9550e-155">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="9550e-156">El :::no-loc(Razor)::: comportamiento de análisis de un `string` valor de tipo no se aplica a un `param-*` atributo porque el atributo es un `object` tipo.</span><span class="sxs-lookup"><span data-stu-id="9550e-156">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="9550e-157">Se admiten todos los tipos de parámetros, excepto:</span><span class="sxs-lookup"><span data-stu-id="9550e-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="9550e-158">Parámetros genéricos.</span><span class="sxs-lookup"><span data-stu-id="9550e-158">Generic parameters.</span></span>
* <span data-ttu-id="9550e-159">Parámetros no serializables.</span><span class="sxs-lookup"><span data-stu-id="9550e-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="9550e-160">Herencia en parámetros de colección.</span><span class="sxs-lookup"><span data-stu-id="9550e-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="9550e-161">Los parámetros cuyo tipo se define fuera de la :::no-loc(Blazor WebAssembly)::: aplicación o dentro de un ensamblado cargado de forma diferida.</span><span class="sxs-lookup"><span data-stu-id="9550e-161">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="9550e-162">El tipo de parámetro debe ser serializable de JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y las propiedades configurables.</span><span class="sxs-lookup"><span data-stu-id="9550e-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="9550e-163">Por ejemplo, puede especificar un valor para `Size` y `Color` en el ejemplo anterior porque los tipos de `Size` y `Color` son tipos primitivos ( `int` y `string` ), que son compatibles con el serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="9550e-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="9550e-164">En el ejemplo siguiente, se pasa un objeto de clase al componente:</span><span class="sxs-lookup"><span data-stu-id="9550e-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="9550e-165">*MyClass.CS* :</span><span class="sxs-lookup"><span data-stu-id="9550e-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="9550e-166">**La clase debe tener un constructor sin parámetros público.**</span><span class="sxs-lookup"><span data-stu-id="9550e-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="9550e-167">*Shared/Component. Razor* :</span><span class="sxs-lookup"><span data-stu-id="9550e-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="9550e-168">*Pages/mi. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9550e-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="9550e-169">En el ejemplo anterior se supone que el `MyComponent` componente está en la carpeta *compartida* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9550e-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="9550e-170">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using :::no-loc(Blazor):::Sample` y `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="9550e-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="9550e-171">`MyClass` está en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9550e-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9550e-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9550e-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
