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
# <a name="aspnet-core-blazor-event-handling"></a>Control de eventos de Blazor en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes de Razor proporcionan características de control de eventos. Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.

El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:

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

El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>. No es necesario llamar a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) de forma manual. Las excepciones se registran cuando se producen.

En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:

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

## <a name="event-argument-types"></a>Tipos de argumento de evento

En algunos eventos, se permiten los tipos de argumento de evento. La especificación de un parámetro de evento en una definición de método de evento es opcional y solo es necesario si se usa el tipo de evento en el método. En el ejemplo siguiente, el argumento de evento `MouseEventArgs` se usa en el método `ShowMessage` para establecer el texto del mensaje:

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

En la tabla siguiente se muestran los valores <xref:System.EventArgs> admitidos.

| evento            | Clase                | Eventos y notas de DOM |
| ---------------- | -------------------- | -------------------- |
| Portapapeles        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Arrastrar             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados. |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| evento            | <xref:System.EventArgs> | *General*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Portapapeles*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrada*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit><br><br>*Elementos multimedia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento. |
| Foco            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>No incluye compatibilidad con `relatedTarget`. |
| Entrada            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Teclado         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Puntero del mouse    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Rueda del mouse      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Progreso         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Entrada táctil            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto. |

Para obtener más información, vea los siguientes recursos:

* [Clases `EventArgs` en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): incluye información sobre qué elementos HTML admite cada evento DOM.

## <a name="lambda-expressions"></a>Expresiones lambda

También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos. En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:

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
> **No** use una variable de bucle directamente en una expresión lambda (como `i` en el ejemplo de bucle `for` anterior). de lo contrario, todas las expresiones lambda usarán la misma variable, con lo cual se usará el mismo valor en todas las expresiones lambda. Capture siempre el valor de la variable en una variable local y úsela. En el ejemplo anterior, la variable de bucle `i` se asigna a `buttonNumber`.

## <a name="eventcallback"></a>EventCallback

Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de un componente secundario. Un caso habitual es un evento `onclick` que se produce en el componente secundario. Para exponer eventos entre componentes, use un elemento <xref:Microsoft.AspNetCore.Components.EventCallback>. Un componente primario puede asignar un método de devolución de llamada al elemento <xref:Microsoft.AspNetCore.Components.EventCallback> de un componente secundario.

El elemento `ChildComponent` de la aplicación de ejemplo (`Components/ChildComponent.razor`) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de <xref:Microsoft.AspNetCore.Components.EventCallback> del objeto `ParentComponent` del ejemplo. El elemento <xref:Microsoft.AspNetCore.Components.EventCallback> tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` establece el objeto <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.

`Pages/ParentComponent.razor`:

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

Cuando el botón se selecciona en `ChildComponent`:

* Se llama al método `ShowMessage` de `ParentComponent`. `messageText` se actualiza y se muestra en `ParentComponent`.
* No se requiere una llamada a [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`). Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.

<xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> permiten delegados asincrónicos. <xref:Microsoft.AspNetCore.Components.EventCallback> tiene un establecimiento flexible de tipos y permite pasar cualquier argumento de tipo en `InvokeAsync(Object)`. <xref:Microsoft.AspNetCore.Components.EventCallback%601> tiene un establecimiento inflexible de tipos y requiere pasar un argumento `T` en `InvokeAsync(T)` que se puede asignar a `TValue`.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Invoque <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> y espere a <xref:System.Threading.Tasks.Task>:

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Use <xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> para el control de eventos y el enlace de parámetros de componentes.

Se prefiere <xref:Microsoft.AspNetCore.Components.EventCallback%601> (fuertemente tipado) a <xref:Microsoft.AspNetCore.Components.EventCallback>. <xref:Microsoft.AspNetCore.Components.EventCallback%601> proporciona mejores comentarios de errores a los usuarios del componente. Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional. Use <xref:Microsoft.AspNetCore.Components.EventCallback> cuando no se pase ningún valor a la devolución de llamada.

## <a name="prevent-default-actions"></a>Bloqueo de acciones predeterminadas

Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.

Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto. En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`. El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:

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

Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.

El valor del atributo también puede ser una expresión. En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>Detención de la propagación de eventos

Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.

En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:

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
