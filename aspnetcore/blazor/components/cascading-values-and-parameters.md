---
title: Valores y parámetros en cascada de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo transferir datos desde un componente antecesor a componentes descendientes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
ms.openlocfilehash: 56d70cea50a3a913b4483f6ea488438269aa58fe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507985"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a><span data-ttu-id="2fa23-103">Valores y parámetros en cascada de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fa23-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="2fa23-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2fa23-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2fa23-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fa23-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2fa23-106">En algunos escenarios, no es conveniente que los datos fluyan desde un componente antecesor a un componente descendiente usando [parámetros de componente](xref:blazor/components/index#component-parameters), sobre todo si hay varios niveles de componentes.</span><span class="sxs-lookup"><span data-stu-id="2fa23-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="2fa23-107">Los valores y parámetros en cascada ponen fin a este problema, ya que constituyen un método cómodo para que un componente antecesor proporcione un valor a todos sus componentes descendientes.</span><span class="sxs-lookup"><span data-stu-id="2fa23-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="2fa23-108">Los valores y parámetros en cascada también sirven para que los componentes se coordinen.</span><span class="sxs-lookup"><span data-stu-id="2fa23-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="2fa23-109">Ejemplo de Theme</span><span class="sxs-lookup"><span data-stu-id="2fa23-109">Theme example</span></span>

<span data-ttu-id="2fa23-110">En el siguiente ejemplo de la aplicación de muestra, la clase `ThemeInfo` especifica la información de tema que va a fluir hacia abajo en la jerarquía de componentes para que todos los botones de una parte determinada de la aplicación compartan el mismo estilo.</span><span class="sxs-lookup"><span data-stu-id="2fa23-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="2fa23-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="2fa23-112">Un componente antecesor puede proporcionar un valor en cascada mediante el componente CascadingValue.</span><span class="sxs-lookup"><span data-stu-id="2fa23-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="2fa23-113">El componente <xref:Microsoft.AspNetCore.Components.CascadingValue%601> encapsula un subárbol de la jerarquía de componentes y proporciona un mismo valor para todos los componentes de ese subárbol.</span><span class="sxs-lookup"><span data-stu-id="2fa23-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="2fa23-114">Por ejemplo, en la aplicación de muestra se especifica información de tema (`ThemeInfo`) en uno de los diseños de la aplicación como un parámetro en cascada de todos los componentes que componen el cuerpo del diseño de la propiedad `@Body`.</span><span class="sxs-lookup"><span data-stu-id="2fa23-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="2fa23-115">A `ButtonClass` se le asigna un valor `btn-success` en el componente de diseño.</span><span class="sxs-lookup"><span data-stu-id="2fa23-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="2fa23-116">Cualquier componente descendiente puede usar esta propiedad a lo largo del objeto en cascada `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="2fa23-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="2fa23-117">Componente `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-117">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="2fa23-118">Para usar valores en cascada, los componentes declaran parámetros en cascada mediante el atributo [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="2fa23-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="2fa23-119">Los valores en cascada se enlazan a los parámetros en cascada según el tipo.</span><span class="sxs-lookup"><span data-stu-id="2fa23-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="2fa23-120">En la aplicación de muestra, el componente `CascadingValuesParametersTheme` enlaza el valor en cascada `ThemeInfo` a un parámetro en cascada.</span><span class="sxs-lookup"><span data-stu-id="2fa23-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="2fa23-121">El parámetro se usa para establecer la clase CSS para uno de los botones que el componente muestra.</span><span class="sxs-lookup"><span data-stu-id="2fa23-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="2fa23-122">Componente `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-122">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="2fa23-123">Para poner en cascada varios valores del mismo tipo en un mismo subárbol, proporcione una cadena <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> única en cada componente <xref:Microsoft.AspNetCore.Components.CascadingValue%601> y su correspondiente atributo [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="2fa23-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="2fa23-124">En el siguiente ejemplo, dos componentes <xref:Microsoft.AspNetCore.Components.CascadingValue%601> en cascada son instancias distintas de `MyCascadingType` por su nombre:</span><span class="sxs-lookup"><span data-stu-id="2fa23-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="2fa23-125">En un componente descendiente, los parámetros en cascada reciben sus valores de los valores en cascada correspondientes del componente antecesor por su nombre:</span><span class="sxs-lookup"><span data-stu-id="2fa23-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="2fa23-126">Ejemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="2fa23-126">TabSet example</span></span>

<span data-ttu-id="2fa23-127">Los parámetros en cascada también permiten que los componentes colaboren a lo largo de la jerarquía de componentes.</span><span class="sxs-lookup"><span data-stu-id="2fa23-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="2fa23-128">Por ejemplo, valore el siguiente ejemplo de `TabSet` en la aplicación de muestra.</span><span class="sxs-lookup"><span data-stu-id="2fa23-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="2fa23-129">La aplicación de muestra tiene una interfaz `ITab` que las pestañas implementan:</span><span class="sxs-lookup"><span data-stu-id="2fa23-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/5.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="2fa23-130">El componente `CascadingValuesParametersTabSet` usa el componente `TabSet`, que contiene varios componentes `Tab`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
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

<span data-ttu-id="2fa23-131">Los componentes `Tab` secundarios no se pasan explícitamente como parámetros a `TabSet`,</span><span class="sxs-lookup"><span data-stu-id="2fa23-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="2fa23-132">sino que forman parte del contenido secundario de `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="2fa23-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="2fa23-133">Pese a ello, `TabSet` sigue necesitando saber de cada componente `Tab` para poder representar los encabezados y la pestaña activa. Para permitir esta coordinación sin requerir código extra, el componente `TabSet` *puede proporcionarse a sí mismo como un valor en cascada* que, luego, van a seleccionar los componentes `Tab` descendientes.</span><span class="sxs-lookup"><span data-stu-id="2fa23-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="2fa23-134">Componente `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="2fa23-135">Los componentes `Tab` descendientes capturan el elemento `TabSet` contenedor como un parámetro en cascada, por lo que los componentes `Tab` se agregan a sí mismo a `TabSet` y coordinan en qué pestaña está activo.</span><span class="sxs-lookup"><span data-stu-id="2fa23-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="2fa23-136">Componente `Tab`:</span><span class="sxs-lookup"><span data-stu-id="2fa23-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/Tab.razor)]
