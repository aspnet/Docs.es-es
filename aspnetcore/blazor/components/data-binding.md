---
title: Enlace de datos de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las características de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 1f22468c2c9bfb5c3c184e8686f1a8d1b486536d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486075"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="f86e4-103">Enlace de datos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f86e4-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="f86e4-104">De [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Steve Sanderson](https://github.com/SteveSandersonMS).</span><span class="sxs-lookup"><span data-stu-id="f86e4-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="f86e4-105">Los componentes de Razor proporcionan características de enlace de datos por medio de un atributo de elemento HTML denominado [`@bind`](xref:mvc/views/razor#bind) con un valor de campo, propiedad o expresión de Razor.</span><span class="sxs-lookup"><span data-stu-id="f86e4-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="f86e4-106">En el ejemplo siguiente se enlaza un elemento `<input>` al campo `currentValue` y un elemento `<input>` a la propiedad `CurrentValue`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f86e4-107">Cuando uno de los elementos pierde el foco, se actualiza su campo o propiedad enlazada.</span><span class="sxs-lookup"><span data-stu-id="f86e4-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="f86e4-108">El cuadro de texto se actualiza en la interfaz de usuario solo cuando se representa el componente, no en respuesta al cambio de valor del campo o la propiedad.</span><span class="sxs-lookup"><span data-stu-id="f86e4-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="f86e4-109">Como los componentes se representan a sí mismos después de que se ejecute el código del controlador de eventos, las actualizaciones de campos y propiedades *normalmente* se reflejan en la interfaz de usuario justo después de que se desencadene un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="f86e4-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="f86e4-110">Usar [`@bind`](xref:mvc/views/razor#bind) con la propiedad `CurrentValue` (`<input @bind="CurrentValue" />`) es prácticamente igual que usar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f86e4-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f86e4-111">Cuando se representa el componente, el valor (`value`) del elemento de entrada procede de la propiedad `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="f86e4-112">Cuando el usuario escribe en el cuadro de texto y cambia el foco del elemento, se desencadena el evento `onchange` y la propiedad `CurrentValue` se establece en el valor modificado.</span><span class="sxs-lookup"><span data-stu-id="f86e4-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="f86e4-113">En realidad, la generación de código es más compleja, porque [`@bind`](xref:mvc/views/razor#bind) controla casos en los que se realizan conversiones de tipos.</span><span class="sxs-lookup"><span data-stu-id="f86e4-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="f86e4-114">En principio, [`@bind`](xref:mvc/views/razor#bind) asocia el valor actual de una expresión con un atributo `value` y controla los cambios mediante el controlador registrado.</span><span class="sxs-lookup"><span data-stu-id="f86e4-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="f86e4-115">También puede enlazar una propiedad o un campo con otros eventos incluyendo un atributo `@bind:event` con un parámetro `event`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="f86e4-116">En el siguiente ejemplo se enlaza la propiedad `CurrentValue` en el evento `oninput`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f86e4-117">A diferencia de `onchange`, que se activa cuando el elemento pierde el foco, `oninput` se desencadena cuando cambia el valor del cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="f86e4-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="f86e4-118">El enlace de atributos distingue mayúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="f86e4-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="f86e4-119">`@bind` es válido.</span><span class="sxs-lookup"><span data-stu-id="f86e4-119">`@bind` is valid.</span></span>
* <span data-ttu-id="f86e4-120">`@Bind` ni `@BIND` son válidos.</span><span class="sxs-lookup"><span data-stu-id="f86e4-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="f86e4-121">Valores no analizables</span><span class="sxs-lookup"><span data-stu-id="f86e4-121">Unparsable values</span></span>

<span data-ttu-id="f86e4-122">Cuando un usuario proporciona un valor no analizable a un elemento enlazado a datos, el valor no analizable se revierte automáticamente a su valor anterior al desencadenar el evento de enlace.</span><span class="sxs-lookup"><span data-stu-id="f86e4-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="f86e4-123">Considere el caso siguiente:</span><span class="sxs-lookup"><span data-stu-id="f86e4-123">Consider the following scenario:</span></span>

* <span data-ttu-id="f86e4-124">Un elemento `<input>` se enlaza a un tipo `int` con un valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="f86e4-125">El usuario actualiza el valor del elemento a `123.45` en la página y cambia el foco del elemento.</span><span class="sxs-lookup"><span data-stu-id="f86e4-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="f86e4-126">En el escenario anterior, el valor del elemento se revierte a `123`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="f86e4-127">Cuando el valor `123.45` se rechaza en favor del valor original de `123`, el usuario entiende que no se ha aceptado su valor.</span><span class="sxs-lookup"><span data-stu-id="f86e4-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="f86e4-128">De forma predeterminada, el enlace de datos se aplica al evento `onchange` del elemento (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="f86e4-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="f86e4-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para desencadenar el enlace en un evento diferente.</span><span class="sxs-lookup"><span data-stu-id="f86e4-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="f86e4-130">En el caso del evento `oninput` (`@bind:event="oninput"`), la reversión se produce después de cualquier pulsación de tecla que introduzca un valor no analizable.</span><span class="sxs-lookup"><span data-stu-id="f86e4-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="f86e4-131">Cuando el destino es el evento `oninput` con un tipo enlazado a `int`, se impide que el usuario escriba el carácter `.`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="f86e4-132">El carácter `.` se elimina de forma inmediata, por lo que el usuario recibe un comentario al instante informándole de que solo se permiten números enteros.</span><span class="sxs-lookup"><span data-stu-id="f86e4-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="f86e4-133">Hay escenarios en los que la reversión del valor del evento `oninput` no es ideal, por ejemplo, cuando se debe permitir que el usuario borre un valor `<input>` que no se puede analizar.</span><span class="sxs-lookup"><span data-stu-id="f86e4-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="f86e4-134">De forma alternativa, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f86e4-134">Alternatives include:</span></span>

* <span data-ttu-id="f86e4-135">No use el evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="f86e4-136">Use el evento `onchange` predeterminado (especifique solo `@bind="{PROPERTY OR FIELD}"`) para que no se revierta un valor no válido hasta que el elemento pierda el foco.</span><span class="sxs-lookup"><span data-stu-id="f86e4-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="f86e4-137">Enlace a un tipo que acepte valores NULL, como `int?` o `string`, y proporcione lógica personalizada para administrar las entradas no válidas.</span><span class="sxs-lookup"><span data-stu-id="f86e4-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="f86e4-138">Use un [componente de validación de formularios](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> o <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="f86e4-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="f86e4-139">Los componentes de validación de formularios tienen compatibilidad integrada para administrar entradas no válidas.</span><span class="sxs-lookup"><span data-stu-id="f86e4-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="f86e4-140">Para obtener más información, vea <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="f86e4-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="f86e4-141">Componentes de validación de formularios:</span><span class="sxs-lookup"><span data-stu-id="f86e4-141">Form validation components:</span></span>
  * <span data-ttu-id="f86e4-142">Permiten que el usuario proporcione entradas no válidas y reciba errores de validación en la clase <xref:Microsoft.AspNetCore.Components.Forms.EditContext> asociada.</span><span class="sxs-lookup"><span data-stu-id="f86e4-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="f86e4-143">Muestran errores de validación en la interfaz de usuario sin impedir que el usuario escriba datos adicionales en el formulario web.</span><span class="sxs-lookup"><span data-stu-id="f86e4-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="f86e4-144">Cadenas de formato</span><span class="sxs-lookup"><span data-stu-id="f86e4-144">Format strings</span></span>

<span data-ttu-id="f86e4-145">El enlace de datos funciona con cadenas de formato <xref:System.DateTime> mediante `@bind:format`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="f86e4-146">Otras expresiones de formato, como los formatos de moneda o número, no están disponibles en este momento.</span><span class="sxs-lookup"><span data-stu-id="f86e4-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="f86e4-147">En el código anterior, el tipo de campo (`type`) del elemento `<input>` tiene como valor predeterminado `text`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="f86e4-148">`@bind:format` se admite para enlazar los siguientes tipos de .NET:</span><span class="sxs-lookup"><span data-stu-id="f86e4-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="f86e4-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f86e4-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="f86e4-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f86e4-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="f86e4-151">El atributo `@bind:format` especifica el formato de fecha que se va a aplicar al valor (`value`) del elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="f86e4-152">El formato también se usa para analizar el valor cuando se produce un evento `onchange`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="f86e4-153">No se recomienda especificar un formato para el tipo de campo `date` porque Blazor tiene compatibilidad integrada para dar formato a las fechas.</span><span class="sxs-lookup"><span data-stu-id="f86e4-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="f86e4-154">A pesar de la recomendación, use solo el formato de fecha `yyyy-MM-dd` para que el enlace funcione correctamente si se proporciona un formato con el tipo de campo `date`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="f86e4-155">Enlace con parámetros de componente</span><span class="sxs-lookup"><span data-stu-id="f86e4-155">Binding with component parameters</span></span>

<span data-ttu-id="f86e4-156">Un escenario habitual es enlazar una propiedad de un componente secundario a una propiedad de su elemento primario.</span><span class="sxs-lookup"><span data-stu-id="f86e4-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="f86e4-157">Este escenario se denomina *enlace encadenado* porque se producen varios niveles de enlace simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="f86e4-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="f86e4-158">Los [parámetros de componente](xref:blazor/components/index#component-parameters) permiten el enlace de propiedades y campos de un componente primario con la sintaxis `@bind-{PROPERTY OR FIELD}`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-158">[Component parameters](xref:blazor/components/index#component-parameters) permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="f86e4-159">No se pueden implementar enlaces encadenados con sintaxis [`@bind`](xref:mvc/views/razor#bind) en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f86e4-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="f86e4-160">Es necesario especificar un controlador de eventos y un valor por separado para permitir la actualización de la propiedad en el elemento primario desde el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f86e4-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="f86e4-161">El componente primario sigue aprovechando la sintaxis [`@bind`](xref:mvc/views/razor#bind) para configurar el enlace de datos con el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f86e4-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="f86e4-162">El siguiente componente `Child` (`Shared/Child.razor`) tiene un parámetro de componente `Year` y la devolución de llamada `YearChanged`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="f86e4-163">La devolución de llamada (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) debe tener el nombre del parámetro del componente seguido del sufijo "`Changed`" (`{PARAMETER NAME}Changed`).</span><span class="sxs-lookup"><span data-stu-id="f86e4-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="f86e4-164">En el ejemplo anterior, el nombre de la devolución de llamada es `YearChanged`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="f86e4-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invoca al delegado asociado al enlace con el argumento proporcionado y envía una notificación de evento de la propiedad modificada.</span><span class="sxs-lookup"><span data-stu-id="f86e4-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="f86e4-166">En el siguiente componente `Parent` (`Parent.razor`), el campo `year` se enlaza al parámetro `Year` del componente secundario:</span><span class="sxs-lookup"><span data-stu-id="f86e4-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="f86e4-167">El parámetro `Year` se puede enlazar porque tiene un evento `YearChanged` complementario que coincide con el tipo del parámetro `Year`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="f86e4-168">Por convención, una propiedad se puede enlazar a un controlador de eventos correspondiente si se incluye un atributo `@bind-{PROPERTY}:event` asignado al controlador.</span><span class="sxs-lookup"><span data-stu-id="f86e4-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="f86e4-169">`<Child @bind-Year="year" />` equivale a escribir lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f86e4-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="f86e4-170">En un ejemplo real más sofisticado, el siguiente componente `PasswordField` (`PasswordField.razor`):</span><span class="sxs-lookup"><span data-stu-id="f86e4-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="f86e4-171">Establece el valor del elemento `<input>` en un campo `password`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="f86e4-172">Expone los cambios de una propiedad `Password` a un componente primario con un objeto [`EventCallback`](xref:blazor/components/event-handling#eventcallback) que pasa el valor actual del campo `password` del elemento secundario como su argumento.</span><span class="sxs-lookup"><span data-stu-id="f86e4-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="f86e4-173">Usa el evento `onclick` para desencadenar el método `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="f86e4-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="f86e4-174">Para obtener más información, vea <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="f86e4-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="f86e4-175">El componente `PasswordField` se usa en otro componente:</span><span class="sxs-lookup"><span data-stu-id="f86e4-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="f86e4-176">Realice comprobaciones o errores de captura en el método que invoca el delegado del enlace.</span><span class="sxs-lookup"><span data-stu-id="f86e4-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="f86e4-177">El ejemplo siguiente informa de inmediato al usuario si se usa un espacio en el valor de la contraseña:</span><span class="sxs-lookup"><span data-stu-id="f86e4-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="f86e4-178">Para más información sobre <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="f86e4-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="f86e4-179">Enlace entre más de dos componentes</span><span class="sxs-lookup"><span data-stu-id="f86e4-179">Bind across more than two components</span></span>

<span data-ttu-id="f86e4-180">Puede enlazar a través de cualquier número de componentes anidados, pero debe respetar el flujo unidireccional de datos:</span><span class="sxs-lookup"><span data-stu-id="f86e4-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="f86e4-181">Las notificaciones de cambio *suben en la jerarquía*.</span><span class="sxs-lookup"><span data-stu-id="f86e4-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="f86e4-182">Los valores nuevos de parámetro *bajan en la jerarquía*.</span><span class="sxs-lookup"><span data-stu-id="f86e4-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="f86e4-183">Un enfoque común y recomendado es almacenar solo los datos subyacentes en el componente primario para evitar la confusión sobre el estado que se debe actualizar.</span><span class="sxs-lookup"><span data-stu-id="f86e4-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="f86e4-184">Los siguientes componentes demuestran los conceptos anteriores:</span><span class="sxs-lookup"><span data-stu-id="f86e4-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="f86e4-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="f86e4-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="f86e4-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f86e4-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="f86e4-188">Para obtener un enfoque alternativo adecuado para compartir datos en memoria entre componentes que no están necesariamente anidados, consulte la sección *Servicio de contenedor de estado en memoria* de <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="f86e4-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see the *In-memory state container service* section of  <xref:blazor/state-management>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f86e4-189">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f86e4-189">Additional resources</span></span>

* [<span data-ttu-id="f86e4-190">Enlace a botones de radio en un formulario</span><span class="sxs-lookup"><span data-stu-id="f86e4-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="f86e4-191">Enlace de opciones del elemento `<select>` a valores `null` de un objeto C# en un formulario</span><span class="sxs-lookup"><span data-stu-id="f86e4-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
