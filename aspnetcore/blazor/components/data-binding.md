---
title: Enlace de datos de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las características de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 35873e57171b4d86affcb475ad2d55aef443d3b5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399184"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="df73c-103">Enlace de datos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="df73c-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="df73c-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df73c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="df73c-105">Los componentes de Razor proporcionan características de enlace de datos por medio de un atributo de elemento HTML denominado [`@bind`](xref:mvc/views/razor#bind) con un valor de campo, propiedad o expresión de Razor.</span><span class="sxs-lookup"><span data-stu-id="df73c-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="df73c-106">En el ejemplo siguiente se enlaza la propiedad `CurrentValue` al valor del cuadro de texto:</span><span class="sxs-lookup"><span data-stu-id="df73c-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="df73c-107">Cuando el cuadro de texto pierde el foco, se actualiza el valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="df73c-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="df73c-108">El cuadro de texto se actualiza en la interfaz de usuario solo cuando se representa el componente, no en respuesta al cambio de valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="df73c-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="df73c-109">Como los componentes se representan a sí mismos después de que se ejecute el código del controlador de eventos, las actualizaciones de propiedades *normalmente* se reflejan en la interfaz de usuario justo después de que se desencadene un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="df73c-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="df73c-110">Usar [`@bind`](xref:mvc/views/razor#bind) con la propiedad `CurrentValue` (`<input @bind="CurrentValue" />`) es prácticamente igual que usar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df73c-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="df73c-111">Cuando se representa el componente, el valor (`value`) del elemento de entrada procede de la propiedad `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="df73c-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="df73c-112">Cuando el usuario escribe en el cuadro de texto y cambia el foco del elemento, se desencadena el evento `onchange` y la propiedad `CurrentValue` se establece en el valor modificado.</span><span class="sxs-lookup"><span data-stu-id="df73c-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="df73c-113">En realidad, la generación de código es más compleja, porque [`@bind`](xref:mvc/views/razor#bind) administra los casos en los que se realizan conversiones de tipos.</span><span class="sxs-lookup"><span data-stu-id="df73c-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="df73c-114">En principio, [`@bind`](xref:mvc/views/razor#bind) asocia el valor actual de una expresión con un atributo `value` y controla los cambios mediante el controlador registrado.</span><span class="sxs-lookup"><span data-stu-id="df73c-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="df73c-115">También puede enlazar una propiedad o un campo con otros eventos incluyendo un atributo `@bind:event` con un parámetro `event`.</span><span class="sxs-lookup"><span data-stu-id="df73c-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="df73c-116">En el siguiente ejemplo se enlaza la propiedad `CurrentValue` en el evento `oninput`:</span><span class="sxs-lookup"><span data-stu-id="df73c-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="df73c-117">A diferencia de `onchange`, que se activa cuando el elemento pierde el foco, `oninput` se desencadena cuando cambia el valor del cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="df73c-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="df73c-118">Use `@bind-{ATTRIBUTE}` con la sintaxis `@bind-{ATTRIBUTE}:event` para enlazar atributos de elementos que no sean `value`.</span><span class="sxs-lookup"><span data-stu-id="df73c-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="df73c-119">En el ejemplo siguiente, el estilo del párrafo se actualiza cuando cambia el valor de `paragraphStyle`:</span><span class="sxs-lookup"><span data-stu-id="df73c-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="df73c-120">El enlace de atributos distingue mayúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="df73c-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="df73c-121">`@bind` es válido.</span><span class="sxs-lookup"><span data-stu-id="df73c-121">`@bind` is valid.</span></span>
* <span data-ttu-id="df73c-122">`@Bind` ni `@BIND` son válidos.</span><span class="sxs-lookup"><span data-stu-id="df73c-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="df73c-123">Valores no analizables</span><span class="sxs-lookup"><span data-stu-id="df73c-123">Unparsable values</span></span>

<span data-ttu-id="df73c-124">Cuando un usuario proporciona un valor no analizable a un elemento enlazado a datos, el valor no analizable se revierte automáticamente a su valor anterior al desencadenar el evento de enlace.</span><span class="sxs-lookup"><span data-stu-id="df73c-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="df73c-125">Considere el caso siguiente:</span><span class="sxs-lookup"><span data-stu-id="df73c-125">Consider the following scenario:</span></span>

* <span data-ttu-id="df73c-126">Un elemento `<input>` se enlaza a un tipo `int` con un valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="df73c-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="df73c-127">El usuario actualiza el valor del elemento a `123.45` en la página y cambia el foco del elemento.</span><span class="sxs-lookup"><span data-stu-id="df73c-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="df73c-128">En el escenario anterior, el valor del elemento se revierte a `123`.</span><span class="sxs-lookup"><span data-stu-id="df73c-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="df73c-129">Cuando el valor `123.45` se rechaza en favor del valor original de `123`, el usuario entiende que no se ha aceptado su valor.</span><span class="sxs-lookup"><span data-stu-id="df73c-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="df73c-130">De forma predeterminada, el enlace de datos se aplica al evento `onchange` del elemento (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="df73c-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="df73c-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para desencadenar el enlace en un evento diferente.</span><span class="sxs-lookup"><span data-stu-id="df73c-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="df73c-132">En el caso del evento `oninput` (`@bind:event="oninput"`), la reversión se produce después de cualquier pulsación de tecla que introduzca un valor no analizable.</span><span class="sxs-lookup"><span data-stu-id="df73c-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="df73c-133">Cuando el destino es el evento `oninput` con un tipo enlazado a `int`, se impide que el usuario escriba el carácter `.`.</span><span class="sxs-lookup"><span data-stu-id="df73c-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="df73c-134">El carácter `.` se elimina de forma inmediata, por lo que el usuario recibe un comentario al instante informándole de que solo se permiten números enteros.</span><span class="sxs-lookup"><span data-stu-id="df73c-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="df73c-135">Hay escenarios en los que la reversión del valor del evento `oninput` no es ideal, por ejemplo, cuando se debe permitir que el usuario borre un valor `<input>` que no se puede analizar.</span><span class="sxs-lookup"><span data-stu-id="df73c-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="df73c-136">De forma alternativa, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df73c-136">Alternatives include:</span></span>

* <span data-ttu-id="df73c-137">No use el evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="df73c-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="df73c-138">Use el evento `onchange` predeterminado (especifique solo `@bind="{PROPERTY OR FIELD}"`) para que no se revierta un valor no válido hasta que el elemento pierda el foco.</span><span class="sxs-lookup"><span data-stu-id="df73c-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="df73c-139">Enlace a un tipo que acepte valores NULL, como `int?` o `string`, y proporcione una lógica personalizada para administrar las entradas no válidas.</span><span class="sxs-lookup"><span data-stu-id="df73c-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="df73c-140">Use un [componente de validación de formularios](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="df73c-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="df73c-141">Los componentes de validación de formularios tienen compatibilidad integrada para administrar entradas no válidas.</span><span class="sxs-lookup"><span data-stu-id="df73c-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="df73c-142">Componentes de validación de formularios:</span><span class="sxs-lookup"><span data-stu-id="df73c-142">Form validation components:</span></span>
  * <span data-ttu-id="df73c-143">Permiten que el usuario proporcione entradas no válidas y reciba errores de validación en la clase <xref:Microsoft.AspNetCore.Components.Forms.EditContext> asociada.</span><span class="sxs-lookup"><span data-stu-id="df73c-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="df73c-144">Muestran errores de validación en la interfaz de usuario sin impedir que el usuario escriba datos adicionales en el formulario web.</span><span class="sxs-lookup"><span data-stu-id="df73c-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="df73c-145">Cadenas de formato</span><span class="sxs-lookup"><span data-stu-id="df73c-145">Format strings</span></span>

<span data-ttu-id="df73c-146">El enlace de datos funciona con cadenas de formato <xref:System.DateTime> mediante `@bind:format`.</span><span class="sxs-lookup"><span data-stu-id="df73c-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="df73c-147">Otras expresiones de formato, como los formatos de moneda o número, no están disponibles en este momento.</span><span class="sxs-lookup"><span data-stu-id="df73c-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="df73c-148">En el código anterior, el tipo de campo (`type`) del elemento `<input>` tiene como valor predeterminado `text`.</span><span class="sxs-lookup"><span data-stu-id="df73c-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="df73c-149">`@bind:format` se admite para enlazar los siguientes tipos de .NET:</span><span class="sxs-lookup"><span data-stu-id="df73c-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="df73c-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="df73c-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="df73c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="df73c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="df73c-152">El atributo `@bind:format` especifica el formato de fecha que se va a aplicar al valor (`value`) del elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="df73c-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="df73c-153">El formato también se usa para analizar el valor cuando se produce un evento `onchange`.</span><span class="sxs-lookup"><span data-stu-id="df73c-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="df73c-154">No se recomienda especificar un formato para el tipo de campo `date` porque Blazor tiene compatibilidad integrada para dar formato a las fechas.</span><span class="sxs-lookup"><span data-stu-id="df73c-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="df73c-155">A pesar de la recomendación, use solo el formato de fecha `yyyy-MM-dd` para que el enlace funcione correctamente si se proporciona un formato con el tipo de campo `date`:</span><span class="sxs-lookup"><span data-stu-id="df73c-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="df73c-156">Enlace de componentes primarios a secundarios con parámetros de componente</span><span class="sxs-lookup"><span data-stu-id="df73c-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="df73c-157">El enlace reconoce los parámetros de componente, por lo que `@bind-{PROPERTY}` puede enlazar un valor de propiedad de un componente primario con un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="df73c-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="df73c-158">En la sección [Enlace de componentes secundarios a primarios con un enlace encadenado](#child-to-parent-binding-with-chained-bind) se describe como enlazar un elemento secundario con uno primario.</span><span class="sxs-lookup"><span data-stu-id="df73c-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="df73c-159">El siguiente componente secundario (`ChildComponent`) tiene un parámetro de componente `Year` y un evento `YearChanged`:</span><span class="sxs-lookup"><span data-stu-id="df73c-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="df73c-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601> se explica en <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="df73c-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="df73c-161">El siguiente componente primario usa:</span><span class="sxs-lookup"><span data-stu-id="df73c-161">The following parent component uses:</span></span>

* <span data-ttu-id="df73c-162">`ChildComponent` y enlaza el parámetro `ParentYear` desde el elemento primario con el parámetro `Year` en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="df73c-162">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="df73c-163">El evento `onclick` se usa para desencadenar el método `ChangeTheYear`.</span><span class="sxs-lookup"><span data-stu-id="df73c-163">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="df73c-164">Para obtener más información, vea <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="df73c-164">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="df73c-165">Al cargar el componente primario, `ParentComponent`, se produce el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="df73c-165">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="df73c-166">Si el valor de la propiedad `ParentYear` se cambia seleccionando el botón del componente primario (`ParentComponent`), se actualiza la propiedad `Year` del componente secundario (`ChildComponent`).</span><span class="sxs-lookup"><span data-stu-id="df73c-166">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="df73c-167">El nuevo valor de `Year` se representa en la interfaz de usuario junto con el componente primario (`ParentComponent`):</span><span class="sxs-lookup"><span data-stu-id="df73c-167">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="df73c-168">El parámetro `Year` se puede enlazar porque tiene un evento `YearChanged` complementario que coincide con el tipo del parámetro `Year`.</span><span class="sxs-lookup"><span data-stu-id="df73c-168">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="df73c-169">Por convención, `<ChildComponent @bind-Year="ParentYear" />` es equivalente a escribir:</span><span class="sxs-lookup"><span data-stu-id="df73c-169">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="df73c-170">En general, una propiedad se puede enlazar a un controlador de eventos correspondiente si se incluye un atributo `@bind-{PROPRETY}:event`.</span><span class="sxs-lookup"><span data-stu-id="df73c-170">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="df73c-171">Por ejemplo, la propiedad `MyProp` se puede enlazar a `MyEventHandler` mediante los dos atributos siguientes:</span><span class="sxs-lookup"><span data-stu-id="df73c-171">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="df73c-172">Enlace de componentes secundarios a primarios con un enlace encadenado</span><span class="sxs-lookup"><span data-stu-id="df73c-172">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="df73c-173">Un escenario común es encadenar un parámetro enlazado a datos a un elemento de página en la salida del componente.</span><span class="sxs-lookup"><span data-stu-id="df73c-173">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="df73c-174">Este escenario se denomina *enlace encadenado* porque se producen varios niveles de enlace simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="df73c-174">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="df73c-175">No se puede implementar un enlace encadenado con sintaxis [`@bind`](xref:mvc/views/razor#bind) en el elemento de la página.</span><span class="sxs-lookup"><span data-stu-id="df73c-175">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="df73c-176">El controlador de eventos y el valor se deben especificar por separado.</span><span class="sxs-lookup"><span data-stu-id="df73c-176">The event handler and value must be specified separately.</span></span> <span data-ttu-id="df73c-177">Sin embargo, un componente primario puede usar la sintaxis [`@bind`](xref:mvc/views/razor#bind) con el parámetro del componente.</span><span class="sxs-lookup"><span data-stu-id="df73c-177">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="df73c-178">El siguiente componente `PasswordField` (`PasswordField.razor`):</span><span class="sxs-lookup"><span data-stu-id="df73c-178">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="df73c-179">Establece el valor del elemento `<input>` en una propiedad `Password`.</span><span class="sxs-lookup"><span data-stu-id="df73c-179">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="df73c-180">Expone los cambios de la propiedad `Password` en un componente primario con [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="df73c-180">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="df73c-181">Usa el evento `onclick` para desencadenar el método `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="df73c-181">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="df73c-182">Para obtener más información, vea <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="df73c-182">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="df73c-183">El componente `PasswordField` se usa en otro componente:</span><span class="sxs-lookup"><span data-stu-id="df73c-183">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="df73c-184">Para realizar comprobaciones o detectar errores en la contraseña en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="df73c-184">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="df73c-185">Cree un campo de respaldo para `Password` (`password` en el siguiente código de ejemplo).</span><span class="sxs-lookup"><span data-stu-id="df73c-185">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="df73c-186">Realice las comprobaciones o detecte los errores en el establecedor de `Password`.</span><span class="sxs-lookup"><span data-stu-id="df73c-186">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="df73c-187">El ejemplo siguiente informa de inmediato al usuario si se usa un espacio en el valor de la contraseña:</span><span class="sxs-lookup"><span data-stu-id="df73c-187">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="df73c-188">Botones de radio</span><span class="sxs-lookup"><span data-stu-id="df73c-188">Radio buttons</span></span>

<span data-ttu-id="df73c-189">Para obtener información sobre cómo enlazar a botones de radio en un formulario, vea <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="df73c-189">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
