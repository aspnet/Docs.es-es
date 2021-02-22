---
title: Valores y parámetros en cascada de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo transferir datos desde un componente antecesor a componentes descendientes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280240"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="a985c-103">Valores y parámetros en cascada de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a985c-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="a985c-104">Los *valores y parámetros en cascada* proporcionan una manera de conveniente de hacer fluir los datos por una jerarquía de componentes desde un componente antecesor a cualquier número de componentes descendiente.</span><span class="sxs-lookup"><span data-stu-id="a985c-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="a985c-105">A diferencia de los [parámetros de componente](xref:blazor/components/index#component-parameters), los valores y parámetros en cascada no requieren una asignación de atributo para cada componente descendiente en el que se consuman los datos.</span><span class="sxs-lookup"><span data-stu-id="a985c-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="a985c-106">Los valores y parámetros en cascada también permiten que los componentes se coordinen entre sí en una jerarquía de componentes.</span><span class="sxs-lookup"><span data-stu-id="a985c-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="a985c-107">Componente de `CascadingValue`</span><span class="sxs-lookup"><span data-stu-id="a985c-107">`CascadingValue` component</span></span>

<span data-ttu-id="a985c-108">Un componente antecesor proporciona un valor en cascada mediante el componente [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) del marco Blazor, que ajusta un subárbol de una jerarquía de componentes y proporciona un valor único a todos los componentes de su subárbol.</span><span class="sxs-lookup"><span data-stu-id="a985c-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="a985c-109">En el ejemplo siguiente se muestra el flujo de información de tema en la jerarquía de componentes de un componente de diseño para proporcionar una clase de estilo CSS a los botones de los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="a985c-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="a985c-110">La siguiente clase `ThemeInfo` de C# se coloca en una carpeta denominada `UIThemeClasses` y especifica la información de tema.</span><span class="sxs-lookup"><span data-stu-id="a985c-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="a985c-111">En los ejemplos de esta sección, el espacio de nombres de la aplicación es `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="a985c-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="a985c-112">Cuando experimente con el código de su aplicación de ejemplo, cambie el espacio de nombres de la aplicación por el de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a985c-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="a985c-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="a985c-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="a985c-114">El [componente de diseño](xref:blazor/layouts) siguiente especifica información de tema (`ThemeInfo`) como un valor en cascada para todos los componentes que constituyen el cuerpo del diseño de la propiedad <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>.</span><span class="sxs-lookup"><span data-stu-id="a985c-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="a985c-115">Se asigna a `ButtonClass` un valor de [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), que es un estilo de botón de arranque.</span><span class="sxs-lookup"><span data-stu-id="a985c-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="a985c-116">Cualquier componente descendiente de la jerarquía de componentes puede usar la propiedad `ButtonClass` a través del valor en cascada `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="a985c-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="a985c-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="a985c-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="a985c-118">Atributo `[CascadingParameter]`</span><span class="sxs-lookup"><span data-stu-id="a985c-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="a985c-119">Para usar valores en cascada, los componentes descendientes declaran parámetros en cascada mediante el [atributo `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="a985c-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="a985c-120">Los valores en cascada se enlazan a los parámetros en cascada **según el tipo**.</span><span class="sxs-lookup"><span data-stu-id="a985c-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="a985c-121">Más adelante en este artículo, en la sección [Valores múltiples en cascada](#cascade-multiple-values), se describe cómo se ponen en cascada varios valores del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="a985c-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="a985c-122">El componente siguiente enlaza el valor en cascada `ThemeInfo` a un parámetro en cascada y, opcionalmente, usa el mismo nombre de `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="a985c-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="a985c-123">El parámetro se usa para establecer la clase CSS para el botón **`Increment Counter (Themed)`** .</span><span class="sxs-lookup"><span data-stu-id="a985c-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="a985c-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="a985c-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="a985c-125">Valores múltiples en cascada</span><span class="sxs-lookup"><span data-stu-id="a985c-125">Cascade multiple values</span></span>

<span data-ttu-id="a985c-126">Para poner en cascada varios valores del mismo tipo en un mismo subárbol, proporcione una cadena <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> única en cada componente [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) y sus correspondientes [atributos `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="a985c-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="a985c-127">En el siguiente ejemplo, dos componentes [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) ponen en cascada instancias distintas de `CascadingType`:</span><span class="sxs-lookup"><span data-stu-id="a985c-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="a985c-128">En un componente descendiente, los parámetros en cascada reciben sus valores en cascada del componente antecesor en función de <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span><span class="sxs-lookup"><span data-stu-id="a985c-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="a985c-129">Paso de datos en una jerarquía de componentes</span><span class="sxs-lookup"><span data-stu-id="a985c-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="a985c-130">Los parámetros en cascada también permiten que los componentes pasen datos en jerarquía de componentes.</span><span class="sxs-lookup"><span data-stu-id="a985c-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="a985c-131">Considere el siguiente ejemplo de conjunto de pestañas de interfaz de usuario, donde un componente del conjunto de pestañas mantiene una serie de pestañas individuales.</span><span class="sxs-lookup"><span data-stu-id="a985c-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="a985c-132">En los ejemplos de esta sección, el espacio de nombres de la aplicación es `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="a985c-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="a985c-133">Cuando experimente con el código de su aplicación de ejemplo, cambie el espacio de nombres por el de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a985c-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="a985c-134">Cree una interfaz `ITab` que las pestañas implementen en una carpeta denominada `UIInterfaces`.</span><span class="sxs-lookup"><span data-stu-id="a985c-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="a985c-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="a985c-135">`UIInterfaces/ITab.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

<span data-ttu-id="a985c-136">El componente `TabSet` siguiente mantiene un conjunto de pestañas.</span><span class="sxs-lookup"><span data-stu-id="a985c-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="a985c-137">Los componentes `Tab` del conjunto de pestañas, que se crean más adelante en esta sección, proporcionan los elementos de lista (`<li>...</li>`) para la lista (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="a985c-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="a985c-138">Los componentes `Tab` secundarios no se pasan explícitamente como parámetros a `TabSet`,</span><span class="sxs-lookup"><span data-stu-id="a985c-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="a985c-139">sino que forman parte del contenido secundario de `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="a985c-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="a985c-140">Pese a ello, `TabSet` sigue necesitando una referencia a cada componente `Tab` para poder representar los encabezados y la pestaña activa. Para permitir esta coordinación sin requerir código extra, el componente `TabSet` *puede proporcionarse a sí mismo como un valor en cascada* que, luego, van a seleccionar los componentes `Tab` descendientes.</span><span class="sxs-lookup"><span data-stu-id="a985c-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="a985c-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="a985c-141">`Shared/TabSet.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="a985c-142">Los componentes `Tab` descendientes capturan el elemento `TabSet` contenedor como parámetro en cascada.</span><span class="sxs-lookup"><span data-stu-id="a985c-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="a985c-143">Los componentes `Tab` se agregan a sí mismos al elemento `TabSet` y se coordinan para establecer la pestaña activa.</span><span class="sxs-lookup"><span data-stu-id="a985c-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="a985c-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="a985c-144">`Shared/Tab.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

<span data-ttu-id="a985c-145">El componente `ExampleTabSet` siguiente usa el componente `TabSet`, que contiene tres componentes `Tab`.</span><span class="sxs-lookup"><span data-stu-id="a985c-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="a985c-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="a985c-146">`Pages/ExampleTabSet.razor`:</span></span>

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
