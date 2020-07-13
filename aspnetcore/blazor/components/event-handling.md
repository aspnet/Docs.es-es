---
title: Control de eventos de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: f15f7e0fc7ef460cefffd817a7d0fa40c1f919b2
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059804"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="e3e52-103">Control de eventos de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3e52-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="e3e52-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e3e52-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e3e52-105">Los componentes de Razor proporcionan características de control de eventos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-105">Razor components provide event handling features.</span></span> <span data-ttu-id="e3e52-106">Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="e3e52-107">El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="e3e52-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="e3e52-108">El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="e3e52-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="e3e52-109">Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="e3e52-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="e3e52-110">No es necesario llamar a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) de forma manual.</span><span class="sxs-lookup"><span data-stu-id="e3e52-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="e3e52-111">Las excepciones se registran cuando se producen.</span><span class="sxs-lookup"><span data-stu-id="e3e52-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="e3e52-112">En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="e3e52-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="e3e52-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="e3e52-113">Event argument types</span></span>

<span data-ttu-id="e3e52-114">En algunos eventos, se permiten los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="e3e52-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="e3e52-115">La especificación de un parámetro de evento en una definición de método de evento es opcional y solo es necesario si se usa el tipo de evento en el método.</span><span class="sxs-lookup"><span data-stu-id="e3e52-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="e3e52-116">En el ejemplo siguiente, el argumento de evento `MouseEventArgs` se usa en el método `ShowMessage` para establecer el texto del mensaje:</span><span class="sxs-lookup"><span data-stu-id="e3e52-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="e3e52-117">En la tabla siguiente se muestran los valores <xref:System.EventArgs> admitidos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="e3e52-118">evento</span><span class="sxs-lookup"><span data-stu-id="e3e52-118">Event</span></span>            | <span data-ttu-id="e3e52-119">Clase</span><span class="sxs-lookup"><span data-stu-id="e3e52-119">Class</span></span>                | <span data-ttu-id="e3e52-120">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="e3e52-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="e3e52-121">Portapapeles</span><span class="sxs-lookup"><span data-stu-id="e3e52-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="e3e52-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="e3e52-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="e3e52-123">Arrastrar</span><span class="sxs-lookup"><span data-stu-id="e3e52-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="e3e52-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="e3e52-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="e3e52-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="e3e52-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="e3e52-126">Error</span><span class="sxs-lookup"><span data-stu-id="e3e52-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="e3e52-127">evento</span><span class="sxs-lookup"><span data-stu-id="e3e52-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="e3e52-128">*General*</span><span class="sxs-lookup"><span data-stu-id="e3e52-128">*General*</span></span><br><span data-ttu-id="e3e52-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="e3e52-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="e3e52-130">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="e3e52-130">*Clipboard*</span></span><br><span data-ttu-id="e3e52-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="e3e52-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="e3e52-132">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="e3e52-132">*Input*</span></span><br><span data-ttu-id="e3e52-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="e3e52-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="e3e52-134">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="e3e52-134">*Media*</span></span><br><span data-ttu-id="e3e52-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="e3e52-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="e3e52-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="e3e52-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="e3e52-137">Foco</span><span class="sxs-lookup"><span data-stu-id="e3e52-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="e3e52-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="e3e52-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="e3e52-139">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="e3e52-140">Entrada</span><span class="sxs-lookup"><span data-stu-id="e3e52-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="e3e52-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="e3e52-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="e3e52-142">Teclado</span><span class="sxs-lookup"><span data-stu-id="e3e52-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="e3e52-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="e3e52-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="e3e52-144">Mouse</span><span class="sxs-lookup"><span data-stu-id="e3e52-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="e3e52-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="e3e52-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="e3e52-146">Puntero del mouse</span><span class="sxs-lookup"><span data-stu-id="e3e52-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="e3e52-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="e3e52-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="e3e52-148">Rueda del mouse</span><span class="sxs-lookup"><span data-stu-id="e3e52-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="e3e52-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="e3e52-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="e3e52-150">Progreso</span><span class="sxs-lookup"><span data-stu-id="e3e52-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="e3e52-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="e3e52-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="e3e52-152">Entrada táctil</span><span class="sxs-lookup"><span data-stu-id="e3e52-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="e3e52-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="e3e52-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="e3e52-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="e3e52-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="e3e52-155">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="e3e52-155">For more information, see the following resources:</span></span>

* <span data-ttu-id="e3e52-156">[Clases `EventArgs` en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="e3e52-156">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="e3e52-157">[Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): incluye información sobre qué elementos HTML admite cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="e3e52-157">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="e3e52-158">Expresiones lambda</span><span class="sxs-lookup"><span data-stu-id="e3e52-158">Lambda expressions</span></span>

<span data-ttu-id="e3e52-159">También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="e3e52-159">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="e3e52-160">A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-160">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="e3e52-161">En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="e3e52-161">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="e3e52-162">**No** use una variable de bucle directamente en una expresión lambda (como `i` en el ejemplo de bucle `for` anterior).</span><span class="sxs-lookup"><span data-stu-id="e3e52-162">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="e3e52-163">de lo contrario, todas las expresiones lambda usarán la misma variable, con lo cual se usará el mismo valor en todas las expresiones lambda.</span><span class="sxs-lookup"><span data-stu-id="e3e52-163">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="e3e52-164">Capture siempre el valor de la variable en una variable local y úsela.</span><span class="sxs-lookup"><span data-stu-id="e3e52-164">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="e3e52-165">En el ejemplo anterior, la variable de bucle `i` se asigna a `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-165">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="e3e52-166">EventCallback</span><span class="sxs-lookup"><span data-stu-id="e3e52-166">EventCallback</span></span>

<span data-ttu-id="e3e52-167">Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="e3e52-167">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="e3e52-168">Un caso habitual es un evento `onclick` que se produce en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="e3e52-168">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="e3e52-169">Para exponer eventos entre componentes, use un elemento <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="e3e52-169">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="e3e52-170">Un componente primario puede asignar un método de devolución de llamada al elemento <xref:Microsoft.AspNetCore.Components.EventCallback> de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="e3e52-170">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="e3e52-171">El elemento `ChildComponent` de la aplicación de ejemplo (`Components/ChildComponent.razor`) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de <xref:Microsoft.AspNetCore.Components.EventCallback> del objeto `ParentComponent` del ejemplo.</span><span class="sxs-lookup"><span data-stu-id="e3e52-171">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="e3e52-172">El elemento <xref:Microsoft.AspNetCore.Components.EventCallback> tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="e3e52-172">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="e3e52-173">`ParentComponent` establece el objeto <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-173">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="e3e52-174">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e3e52-174">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="e3e52-175">Cuando el botón se selecciona en `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="e3e52-175">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="e3e52-176">Se llama al método `ShowMessage` de `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-176">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="e3e52-177">`messageText` se actualiza y se muestra en `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-177">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="e3e52-178">No se requiere una llamada a [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="e3e52-178">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="e3e52-179">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="e3e52-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="e3e52-180"><xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> permiten delegados asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-180"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="e3e52-181"><xref:Microsoft.AspNetCore.Components.EventCallback> tiene un establecimiento flexible de tipos y permite pasar cualquier argumento de tipo en `InvokeAsync(Object)`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="e3e52-182"><xref:Microsoft.AspNetCore.Components.EventCallback%601> tiene un establecimiento inflexible de tipos y requiere pasar un argumento `T` en `InvokeAsync(T)` que se puede asignar a `TValue`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-182"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="e3e52-183">Invoque <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> y espere a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="e3e52-183">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="e3e52-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> para el control de eventos y el enlace de parámetros de componentes.</span><span class="sxs-lookup"><span data-stu-id="e3e52-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="e3e52-185">Se prefiere <xref:Microsoft.AspNetCore.Components.EventCallback%601> (fuertemente tipado) a <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="e3e52-185">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="e3e52-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> proporciona mejores comentarios de errores a los usuarios del componente.</span><span class="sxs-lookup"><span data-stu-id="e3e52-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="e3e52-187">Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional.</span><span class="sxs-lookup"><span data-stu-id="e3e52-187">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="e3e52-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> cuando no se pase ningún valor a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="e3e52-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="e3e52-189">Bloqueo de acciones predeterminadas</span><span class="sxs-lookup"><span data-stu-id="e3e52-189">Prevent default actions</span></span>

<span data-ttu-id="e3e52-190">Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="e3e52-190">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="e3e52-191">Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="e3e52-191">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="e3e52-192">En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-192">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="e3e52-193">El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="e3e52-193">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="e3e52-194">Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="e3e52-194">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="e3e52-195">El valor del atributo también puede ser una expresión.</span><span class="sxs-lookup"><span data-stu-id="e3e52-195">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="e3e52-196">En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:</span><span class="sxs-lookup"><span data-stu-id="e3e52-196">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="e3e52-197">Detención de la propagación de eventos</span><span class="sxs-lookup"><span data-stu-id="e3e52-197">Stop event propagation</span></span>

<span data-ttu-id="e3e52-198">Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="e3e52-198">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="e3e52-199">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:</span><span class="sxs-lookup"><span data-stu-id="e3e52-199">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
