---
title: Control de eventos de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 56306d305a61d549380e847ab2754fa41130e14e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507816"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="ec84b-103">Control de eventos de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec84b-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="ec84b-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ec84b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ec84b-105">Los componentes de Razor proporcionan características de control de eventos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-105">Razor components provide event handling features.</span></span> <span data-ttu-id="ec84b-106">Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="ec84b-107">El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="ec84b-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="ec84b-108">El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="ec84b-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="ec84b-109">Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="ec84b-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="ec84b-110">No es necesario llamar a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) de forma manual.</span><span class="sxs-lookup"><span data-stu-id="ec84b-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="ec84b-111">Las excepciones se registran cuando se producen.</span><span class="sxs-lookup"><span data-stu-id="ec84b-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="ec84b-112">En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="ec84b-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="ec84b-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="ec84b-113">Event argument types</span></span>

<span data-ttu-id="ec84b-114">En algunos eventos, se permiten los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="ec84b-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="ec84b-115">La especificación de un parámetro de evento en una definición de método de evento es opcional y solo es necesario si se usa el tipo de evento en el método.</span><span class="sxs-lookup"><span data-stu-id="ec84b-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="ec84b-116">En el ejemplo siguiente, el argumento de evento `MouseEventArgs` se usa en el método `ShowMessage` para establecer el texto del mensaje:</span><span class="sxs-lookup"><span data-stu-id="ec84b-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="ec84b-117">En la tabla siguiente se muestran los valores <xref:System.EventArgs> admitidos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="ec84b-118">evento</span><span class="sxs-lookup"><span data-stu-id="ec84b-118">Event</span></span>            | <span data-ttu-id="ec84b-119">Clase</span><span class="sxs-lookup"><span data-stu-id="ec84b-119">Class</span></span>  | <span data-ttu-id="ec84b-120">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="ec84b-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="ec84b-121">Portapapeles</span><span class="sxs-lookup"><span data-stu-id="ec84b-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="ec84b-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="ec84b-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="ec84b-123">Arrastrar</span><span class="sxs-lookup"><span data-stu-id="ec84b-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="ec84b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="ec84b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="ec84b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="ec84b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="ec84b-126">Implemente la función de arrastrar y colocar en las aplicaciones de Blazor mediante la [Interoperabilidad de JS](xref:blazor/call-javascript-from-dotnet) con [Drag and Drop API en HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="ec84b-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="ec84b-127">Error</span><span class="sxs-lookup"><span data-stu-id="ec84b-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="ec84b-128">evento</span><span class="sxs-lookup"><span data-stu-id="ec84b-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="ec84b-129">*General*</span><span class="sxs-lookup"><span data-stu-id="ec84b-129">*General*</span></span><br><span data-ttu-id="ec84b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="ec84b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="ec84b-131">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="ec84b-131">*Clipboard*</span></span><br><span data-ttu-id="ec84b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="ec84b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="ec84b-133">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="ec84b-133">*Input*</span></span><br><span data-ttu-id="ec84b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="ec84b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="ec84b-135">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="ec84b-135">*Media*</span></span><br><span data-ttu-id="ec84b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="ec84b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="ec84b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="ec84b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="ec84b-138">Foco</span><span class="sxs-lookup"><span data-stu-id="ec84b-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="ec84b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="ec84b-140">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="ec84b-141">Entrada</span><span class="sxs-lookup"><span data-stu-id="ec84b-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="ec84b-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="ec84b-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="ec84b-143">Teclado</span><span class="sxs-lookup"><span data-stu-id="ec84b-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="ec84b-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="ec84b-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="ec84b-145">Mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="ec84b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="ec84b-147">Puntero del mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="ec84b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="ec84b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="ec84b-149">Rueda del mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="ec84b-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="ec84b-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="ec84b-151">Progreso</span><span class="sxs-lookup"><span data-stu-id="ec84b-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="ec84b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="ec84b-153">Entrada táctil</span><span class="sxs-lookup"><span data-stu-id="ec84b-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="ec84b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="ec84b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="ec84b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="ec84b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="ec84b-156">evento</span><span class="sxs-lookup"><span data-stu-id="ec84b-156">Event</span></span>            | <span data-ttu-id="ec84b-157">Clase</span><span class="sxs-lookup"><span data-stu-id="ec84b-157">Class</span></span> | <span data-ttu-id="ec84b-158">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="ec84b-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="ec84b-159">Portapapeles</span><span class="sxs-lookup"><span data-stu-id="ec84b-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="ec84b-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="ec84b-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="ec84b-161">Arrastrar</span><span class="sxs-lookup"><span data-stu-id="ec84b-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="ec84b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="ec84b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="ec84b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="ec84b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="ec84b-164">Implemente la función de arrastrar y colocar en las aplicaciones de Blazor mediante la [Interoperabilidad de JS](xref:blazor/call-javascript-from-dotnet) con [Drag and Drop API en HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="ec84b-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="ec84b-165">Error</span><span class="sxs-lookup"><span data-stu-id="ec84b-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="ec84b-166">evento</span><span class="sxs-lookup"><span data-stu-id="ec84b-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="ec84b-167">*General*</span><span class="sxs-lookup"><span data-stu-id="ec84b-167">*General*</span></span><br><span data-ttu-id="ec84b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="ec84b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="ec84b-169">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="ec84b-169">*Clipboard*</span></span><br><span data-ttu-id="ec84b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="ec84b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="ec84b-171">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="ec84b-171">*Input*</span></span><br><span data-ttu-id="ec84b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="ec84b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="ec84b-173">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="ec84b-173">*Media*</span></span><br><span data-ttu-id="ec84b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="ec84b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="ec84b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="ec84b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="ec84b-176">Foco</span><span class="sxs-lookup"><span data-stu-id="ec84b-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="ec84b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="ec84b-178">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="ec84b-179">Entrada</span><span class="sxs-lookup"><span data-stu-id="ec84b-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="ec84b-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="ec84b-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="ec84b-181">Teclado</span><span class="sxs-lookup"><span data-stu-id="ec84b-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="ec84b-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="ec84b-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="ec84b-183">Mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="ec84b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="ec84b-185">Puntero del mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="ec84b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="ec84b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="ec84b-187">Rueda del mouse</span><span class="sxs-lookup"><span data-stu-id="ec84b-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="ec84b-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="ec84b-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="ec84b-189">Progreso</span><span class="sxs-lookup"><span data-stu-id="ec84b-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="ec84b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="ec84b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="ec84b-191">Entrada táctil</span><span class="sxs-lookup"><span data-stu-id="ec84b-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="ec84b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="ec84b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="ec84b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="ec84b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="ec84b-194">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="ec84b-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="ec84b-195">[Clases `EventArgs` en el origen de referencia de ASP.NET Core (rama `master` de dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="ec84b-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="ec84b-196">La rama `master` representa la API en desarrollo de la *siguiente* versión de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ec84b-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="ec84b-197">En la versión actual, seleccione la rama del repositorio de GitHub que sea apropiada (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="ec84b-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="ec84b-198">[Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): incluye información sobre qué elementos HTML admite cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="ec84b-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="ec84b-199">Expresiones lambda</span><span class="sxs-lookup"><span data-stu-id="ec84b-199">Lambda expressions</span></span>

<span data-ttu-id="ec84b-200">También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="ec84b-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="ec84b-201">A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="ec84b-202">En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="ec84b-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="ec84b-203">**No** use una variable de bucle directamente en una expresión lambda (como `i` en el ejemplo de bucle `for` anterior).</span><span class="sxs-lookup"><span data-stu-id="ec84b-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="ec84b-204">de lo contrario, todas las expresiones lambda usarán la misma variable, con lo cual se usará el mismo valor en todas las expresiones lambda.</span><span class="sxs-lookup"><span data-stu-id="ec84b-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="ec84b-205">Capture siempre el valor de la variable en una variable local y úsela.</span><span class="sxs-lookup"><span data-stu-id="ec84b-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="ec84b-206">En el ejemplo anterior, la variable de bucle `i` se asigna a `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="ec84b-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="ec84b-207">EventCallback</span></span>

<span data-ttu-id="ec84b-208">Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="ec84b-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="ec84b-209">Un caso habitual es un evento `onclick` que se produce en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="ec84b-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="ec84b-210">Para exponer eventos entre componentes, use un elemento <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="ec84b-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="ec84b-211">Un componente primario puede asignar un método de devolución de llamada al elemento <xref:Microsoft.AspNetCore.Components.EventCallback> de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="ec84b-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="ec84b-212">El elemento `ChildComponent` de la aplicación de ejemplo (`Components/ChildComponent.razor`) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de <xref:Microsoft.AspNetCore.Components.EventCallback> del objeto `ParentComponent` del ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ec84b-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="ec84b-213">El elemento <xref:Microsoft.AspNetCore.Components.EventCallback> tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="ec84b-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="ec84b-214">`ParentComponent` establece el objeto <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="ec84b-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ec84b-215">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="ec84b-216">Cuando el botón se selecciona en `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="ec84b-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="ec84b-217">Se llama al método `ShowMessage` de `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="ec84b-218">`messageText` se actualiza y se muestra en `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="ec84b-219">No se requiere una llamada a [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="ec84b-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="ec84b-220">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="ec84b-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="ec84b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> permiten delegados asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="ec84b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> tiene un establecimiento flexible de tipos y permite pasar cualquier argumento de tipo en `InvokeAsync(Object)`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="ec84b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> tiene un establecimiento inflexible de tipos y requiere pasar un argumento `T` en `InvokeAsync(T)` que se puede asignar a `TValue`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="ec84b-224">Invoque <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> y espere a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="ec84b-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="ec84b-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> para el control de eventos y el enlace de parámetros de componentes.</span><span class="sxs-lookup"><span data-stu-id="ec84b-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="ec84b-226">Se prefiere <xref:Microsoft.AspNetCore.Components.EventCallback%601> (fuertemente tipado) a <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="ec84b-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="ec84b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> proporciona mejores comentarios de errores a los usuarios del componente.</span><span class="sxs-lookup"><span data-stu-id="ec84b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="ec84b-228">Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional.</span><span class="sxs-lookup"><span data-stu-id="ec84b-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="ec84b-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> cuando no se pase ningún valor a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="ec84b-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="ec84b-230">Bloqueo de acciones predeterminadas</span><span class="sxs-lookup"><span data-stu-id="ec84b-230">Prevent default actions</span></span>

<span data-ttu-id="ec84b-231">Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="ec84b-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="ec84b-232">Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="ec84b-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="ec84b-233">En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="ec84b-234">El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="ec84b-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="ec84b-235">Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="ec84b-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="ec84b-236">El valor del atributo también puede ser una expresión.</span><span class="sxs-lookup"><span data-stu-id="ec84b-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="ec84b-237">En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:</span><span class="sxs-lookup"><span data-stu-id="ec84b-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="ec84b-238">Detención de la propagación de eventos</span><span class="sxs-lookup"><span data-stu-id="ec84b-238">Stop event propagation</span></span>

<span data-ttu-id="ec84b-239">Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="ec84b-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="ec84b-240">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:</span><span class="sxs-lookup"><span data-stu-id="ec84b-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="ec84b-241">Foco en un elemento</span><span class="sxs-lookup"><span data-stu-id="ec84b-241">Focus an element</span></span>

<span data-ttu-id="ec84b-242">Llame a `FocusAsync` en una [referencia de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) para enfocar un elemento del código:</span><span class="sxs-lookup"><span data-stu-id="ec84b-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
