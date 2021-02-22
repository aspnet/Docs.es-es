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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>Valores y parámetros en cascada de Blazor en ASP.NET Core

Los *valores y parámetros en cascada* proporcionan una manera de conveniente de hacer fluir los datos por una jerarquía de componentes desde un componente antecesor a cualquier número de componentes descendiente. A diferencia de los [parámetros de componente](xref:blazor/components/index#component-parameters), los valores y parámetros en cascada no requieren una asignación de atributo para cada componente descendiente en el que se consuman los datos. Los valores y parámetros en cascada también permiten que los componentes se coordinen entre sí en una jerarquía de componentes.

## <a name="cascadingvalue-component"></a>Componente de `CascadingValue`

Un componente antecesor proporciona un valor en cascada mediante el componente [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) del marco Blazor, que ajusta un subárbol de una jerarquía de componentes y proporciona un valor único a todos los componentes de su subárbol.

En el ejemplo siguiente se muestra el flujo de información de tema en la jerarquía de componentes de un componente de diseño para proporcionar una clase de estilo CSS a los botones de los componentes secundarios.

La siguiente clase `ThemeInfo` de C# se coloca en una carpeta denominada `UIThemeClasses` y especifica la información de tema.

> [!NOTE]
> En los ejemplos de esta sección, el espacio de nombres de la aplicación es `BlazorSample`. Cuando experimente con el código de su aplicación de ejemplo, cambie el espacio de nombres de la aplicación por el de la aplicación de ejemplo.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

El [componente de diseño](xref:blazor/layouts) siguiente especifica información de tema (`ThemeInfo`) como un valor en cascada para todos los componentes que constituyen el cuerpo del diseño de la propiedad <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>. Se asigna a `ButtonClass` un valor de [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), que es un estilo de botón de arranque. Cualquier componente descendiente de la jerarquía de componentes puede usar la propiedad `ButtonClass` a través del valor en cascada `ThemeInfo`.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>Atributo `[CascadingParameter]`

Para usar valores en cascada, los componentes descendientes declaran parámetros en cascada mediante el [atributo `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Los valores en cascada se enlazan a los parámetros en cascada **según el tipo**. Más adelante en este artículo, en la sección [Valores múltiples en cascada](#cascade-multiple-values), se describe cómo se ponen en cascada varios valores del mismo tipo.

El componente siguiente enlaza el valor en cascada `ThemeInfo` a un parámetro en cascada y, opcionalmente, usa el mismo nombre de `ThemeInfo`. El parámetro se usa para establecer la clase CSS para el botón **`Increment Counter (Themed)`** .

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>Valores múltiples en cascada

Para poner en cascada varios valores del mismo tipo en un mismo subárbol, proporcione una cadena <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> única en cada componente [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) y sus correspondientes [atributos `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).

En el siguiente ejemplo, dos componentes [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) ponen en cascada instancias distintas de `CascadingType`:

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

En un componente descendiente, los parámetros en cascada reciben sus valores en cascada del componente antecesor en función de <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Paso de datos en una jerarquía de componentes

Los parámetros en cascada también permiten que los componentes pasen datos en jerarquía de componentes. Considere el siguiente ejemplo de conjunto de pestañas de interfaz de usuario, donde un componente del conjunto de pestañas mantiene una serie de pestañas individuales.

> [!NOTE]
> En los ejemplos de esta sección, el espacio de nombres de la aplicación es `BlazorSample`. Cuando experimente con el código de su aplicación de ejemplo, cambie el espacio de nombres por el de la aplicación de ejemplo.

Cree una interfaz `ITab` que las pestañas implementen en una carpeta denominada `UIInterfaces`.

`UIInterfaces/ITab.cs`:

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

El componente `TabSet` siguiente mantiene un conjunto de pestañas. Los componentes `Tab` del conjunto de pestañas, que se crean más adelante en esta sección, proporcionan los elementos de lista (`<li>...</li>`) para la lista (`<ul>...</ul>`).

Los componentes `Tab` secundarios no se pasan explícitamente como parámetros a `TabSet`, sino que forman parte del contenido secundario de `TabSet`. Pese a ello, `TabSet` sigue necesitando una referencia a cada componente `Tab` para poder representar los encabezados y la pestaña activa. Para permitir esta coordinación sin requerir código extra, el componente `TabSet` *puede proporcionarse a sí mismo como un valor en cascada* que, luego, van a seleccionar los componentes `Tab` descendientes.

`Shared/TabSet.razor`:

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

Los componentes `Tab` descendientes capturan el elemento `TabSet` contenedor como parámetro en cascada. Los componentes `Tab` se agregan a sí mismos al elemento `TabSet` y se coordinan para establecer la pestaña activa.

`Shared/Tab.razor`:

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

El componente `ExampleTabSet` siguiente usa el componente `TabSet`, que contiene tres componentes `Tab`.

`Pages/ExampleTabSet.razor`:

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
