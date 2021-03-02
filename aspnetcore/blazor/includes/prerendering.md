---
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
ms.openlocfilehash: c152524e0acd3803bd3b8078f667cce01180e25d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552695"
---
Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador. Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.

Para retrasar las llamadas de interoperabilidad de JavaScript hasta que se establezca la conexión con el explorador, puede usar el [evento de ciclo de vida del componente OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render). Solo se llama a este evento después de que la aplicación se represente por completo y se establezca la conexión con el cliente.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

En el código de ejemplo anterior, proporcione una función de JavaScript `setElementText` dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server). Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> En el ejemplo anterior se modifica el Document Object Model (DOM) directamente solo con fines de demostración. No se recomienda modificar directamente el DOM con JavaScript en la mayoría de los escenarios, ya que JavaScript puede interferir con el seguimiento de cambios de Blazor.

En el componente siguiente se muestra cómo usar la interoperabilidad de JavaScript como parte de la lógica de inicialización de un componente de una manera compatible con la representación previa. El componente muestra que es posible desencadenar una actualización de representación desde dentro de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>. El desarrollador debe evitar la creación de un bucle infinito en este escenario.

Cuando se llama a <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType>, `ElementRef` solo se utiliza en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y no en ningún otro método de ciclo de vida anterior porque no existe ningún elemento de JavaScript hasta después de representar el componente.

Se llama a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) para volver a representar el componente con el nuevo estado obtenido de la llamada de interoperabilidad de JavaScript (para obtener más información, vea <xref:blazor/components/rendering>). El código no crea un bucle infinito porque solo se llama a `StateHasChanged` cuando `null` es `infoFromJs`.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

En el código de ejemplo anterior, proporcione una función de JavaScript `setElementText` dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server). Se llama a la función con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> y se devuelve un valor:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> En el ejemplo anterior se modifica el Document Object Model (DOM) directamente solo con fines de demostración. No se recomienda modificar directamente el DOM con JavaScript en la mayoría de los escenarios, ya que JavaScript puede interferir con el seguimiento de cambios de Blazor.
