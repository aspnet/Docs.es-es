---
title: Aplicación auxiliar de etiquetas de componentes en ASP.NET Core
author: guardrex
ms.author: riande
description: Aprenda a usar la aplicación auxiliar de etiquetas de componentes de ASP.NET Core para representar Razor componentes en páginas y vistas.
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595459"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Aplicación auxiliar de etiquetas de componentes en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

## <a name="prerequisites"></a>Requisitos previos

::: moniker range=">= aspnetcore-5.0"

Siga las instrucciones de la sección de *configuración* para:

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Siga las instrucciones de la sección de *configuración* del <xref:blazor/components/prerendering-and-integration?pivots=server> artículo.

::: moniker-end

## <a name="component-tag-helper"></a>Aplicación auxiliar de etiquetas de componentes

Para representar un componente de una página o vista, use la [aplicación auxiliar de etiquetas de componentes](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` etiqueta).

> [!NOTE]
> La integración de Razor componentes en Razor páginas y aplicaciones MVC en una *Blazor WebAssembly aplicación hospedada* se admite en ASP.net Core en .net 5,0 o posterior.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:

* Se representa previamente en la página.
* Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly los modos de representación de la aplicación se muestran en la tabla siguiente.

| Modo de representación | Descripción |
| ----------- | ----------- |
| `WebAssembly` | Representa un marcador para una Blazor WebAssembly aplicación que se usa para incluir un componente interactivo cuando se carga en el explorador. El componente no está prerepresentado. Esta opción facilita la representación Blazor WebAssembly de diferentes componentes en diferentes páginas. |
| `WebAssemblyPrerendered` | Representa el componente en HTML estático e incluye un marcador para una Blazor WebAssembly aplicación para su uso posterior con el fin de que el componente sea interactivo cuando se carga en el explorador. |

Blazor Server los modos de representación de la aplicación se muestran en la tabla siguiente.

| Modo de representación | Descripción |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server. La salida del componente no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente en HTML estático. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server los modos de representación de la aplicación se muestran en la tabla siguiente.

| Modo de representación | Descripción |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server. La salida del componente no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente en HTML estático. |

::: moniker-end

Entre las características adicionales se incluyen:

* Se permiten varias aplicaciones auxiliares de etiquetas de componentes que representan varios Razor componentes.
* Los componentes no se pueden representar dinámicamente una vez iniciada la aplicación.
* Mientras que las páginas y las vistas pueden utilizar componentes, el opuesto no es cierto. Los componentes no pueden usar características específicas de la página y de la vista, como vistas y secciones parciales. Para usar la lógica de una vista parcial en un componente, se debe factorizar la lógica de vista parcial en un componente.
* No se admite la representación de componentes de servidor desde una página HTML estática.

La siguiente aplicación auxiliar de etiquetas de componentes representa el `Counter` componente en una página o una vista de una Blazor Server aplicación con `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

En el ejemplo anterior se supone que el `Counter` componente está en la carpeta *páginas* de la aplicación. El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Pages` o `@using BlazorSample.Client.Pages` en una solución hospedada Blazor ).

La aplicación auxiliar de etiquetas de componente también puede pasar parámetros a los componentes. Considere el siguiente `ColorfulCheckbox` componente que establece el color y el tamaño de la etiqueta de casilla:

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

Los `Size` `int` parámetros de componente () y `Color` ( `string` ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componentes: [component parameters](xref:blazor/components/index#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

En el ejemplo anterior se supone que el `ColorfulCheckbox` componente está en la carpeta *compartida* de la aplicación. El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample.Shared`).

El siguiente código HTML se representa en la página o la vista:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

El paso de una cadena entrecomillada requiere una [ Razor expresión explícita](xref:mvc/views/razor#explicit-razor-expressions), como se muestra `param-Color` en el ejemplo anterior. El Razor comportamiento de análisis de un `string` valor de tipo no se aplica a un `param-*` atributo porque el atributo es un `object` tipo.

Se admiten todos los tipos de parámetros, excepto:

* Parámetros genéricos.
* Parámetros no serializables.
* Herencia en parámetros de colección.
* Los parámetros cuyo tipo se define fuera de la Blazor WebAssembly aplicación o dentro de un ensamblado cargado de forma diferida.

El tipo de parámetro debe ser serializable de JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y las propiedades configurables. Por ejemplo, puede especificar un valor para `Size` y `Color` en el ejemplo anterior porque los tipos de `Size` y `Color` son tipos primitivos ( `int` y `string` ), que son compatibles con el serializador JSON.

En el ejemplo siguiente, se pasa un objeto de clase al componente:

*MyClass.CS* :

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

**La clase debe tener un constructor sin parámetros público.**

*Shared/Component. Razor* :

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

*Pages/mi. cshtml* :

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

En el ejemplo anterior se supone que el `MyComponent` componente está en la carpeta *compartida* de la aplicación. El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `@using BlazorSample` y `@using BlazorSample.Shared` ). `MyClass` está en el espacio de nombres de la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
