---
title: Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5c22220871fdba7fea43c38fa4bc826c07135ffc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060038"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="fd7d6-103">Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd7d6-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="fd7d6-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fd7d6-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd7d6-105">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="fd7d6-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="fd7d6-107">En este artículo se describe cómo invocar funciones de JavaScript desde .NET.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="fd7d6-108">Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="fd7d6-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fd7d6-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fd7d6-110">Para llamar a JavaScript desde .NET, use la abstracción <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="fd7d6-111">Para emitir llamadas de interoperabilidad de JS, inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="fd7d6-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> toma un identificador de la función de JavaScript que se quiere invocar junto con un número cualquiera de argumentos serializables con JSON.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="fd7d6-113">El identificador de función es relativo al ámbito global (`window`).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="fd7d6-114">Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="fd7d6-115">No es necesario registrar la función para poder llamarla.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="fd7d6-116">El tipo de valor devuelto `T` también debe ser serializable con JSON.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="fd7d6-117">`T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="fd7d6-118">En el caso de las aplicaciones Blazor Server que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="fd7d6-119">Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="fd7d6-120">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="fd7d6-121">El siguiente ejemplo se basa en [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="fd7d6-122">En el ejemplo se muestra cómo invocar una función de JavaScript desde un método de C# que descarga un requisito del código de desarrollador a una API de JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="fd7d6-123">La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="fd7d6-124">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="fd7d6-125">El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript (`.js`) con una referencia al archivo de script:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="fd7d6-126">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-126">The following component:</span></span>

* <span data-ttu-id="fd7d6-127">Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="fd7d6-128">Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena,</span><span class="sxs-lookup"><span data-stu-id="fd7d6-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="fd7d6-129">que se devuelve al componente para que la muestre.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="fd7d6-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="fd7d6-130">IJSRuntime</span></span>

<span data-ttu-id="fd7d6-131">Para usar la abstracción <xref:Microsoft.JSInterop.IJSRuntime>, adopte cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="fd7d6-132">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente de Razor (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="fd7d6-133">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fd7d6-134">Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="fd7d6-135">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en una clase (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="fd7d6-136">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fd7d6-137">Se llama a la función con `JSRuntime.InvokeAsync` y se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="fd7d6-138">Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="fd7d6-139">En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="fd7d6-140">`showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="fd7d6-141">`displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="fd7d6-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="fd7d6-143">Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo `wwwroot/index.html` (Blazor WebAssembly) o en el archivo `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="fd7d6-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="fd7d6-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="fd7d6-146">No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="fd7d6-147">Los métodos de .NET interoperan con las funciones de JavaScript en el archivo `exampleJsInterop.js` llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="fd7d6-148">La abstracción <xref:Microsoft.JSInterop.IJSRuntime> es asincrónica para dar cabida a escenarios de servidor Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="fd7d6-149">Si la aplicación es una aplicación Blazor WebAssembly y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado <xref:Microsoft.JSInterop.IJSInProcessRuntime> y llame a <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> en su lugar.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="fd7d6-150">Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="fd7d6-151">La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="fd7d6-152">El componente:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-152">The component:</span></span>

* <span data-ttu-id="fd7d6-153">Recibe la entrada del usuario a través de un mensaje de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="fd7d6-154">Devuelve el texto al componente para que lo procese.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="fd7d6-155">Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="fd7d6-156">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-156">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="fd7d6-157">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-157">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="fd7d6-158">Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **`Trigger JavaScript Prompt`** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-158">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="fd7d6-159">La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="fd7d6-160">El componente almacena el nombre del usuario en una variable local, `name`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="fd7d6-161">La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="fd7d6-162">Llamada a una función de JavaScript vacía</span><span class="sxs-lookup"><span data-stu-id="fd7d6-162">Call a void JavaScript function</span></span>

<span data-ttu-id="fd7d6-163">Las funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) se llaman con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="fd7d6-164">Detección de cuándo se está obteniendo una representación previa de la aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="fd7d6-164">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="fd7d6-165">Captura de referencias a elementos</span><span class="sxs-lookup"><span data-stu-id="fd7d6-165">Capture references to elements</span></span>

<span data-ttu-id="fd7d6-166">Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="fd7d6-167">Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="fd7d6-168">Use el siguiente método para capturar referencias a elementos HTML en un componente:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="fd7d6-169">Agregue un atributo `@ref` al elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="fd7d6-170">Defina un campo de tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cuyo nombre coincida con el valor del atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-170">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="fd7d6-171">En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="fd7d6-172">Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="fd7d6-173">Este escenario es útil cuando una API de terceros proporciona contenido al elemento.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="fd7d6-174">Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="fd7d6-175">En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="fd7d6-176">Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="fd7d6-177">En lo que respecta al código .NET, un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> es un identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-177">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="fd7d6-178">*Lo único* que se puede hacer con <xref:Microsoft.AspNetCore.Components.ElementReference> es pasarlo a código de JavaScript a través de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-178">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="fd7d6-179">Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="fd7d6-180">Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="fd7d6-181">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-181">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="fd7d6-182">Para llamar a una función de JavaScript que no devuelve un valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-182">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fd7d6-183">El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento <xref:Microsoft.AspNetCore.Components.ElementReference> capturado:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="fd7d6-184">Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-184">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="fd7d6-185">Se llama al método `Focus` directamente en el objeto.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="fd7d6-186">En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="fd7d6-187">La variable `username` solo se rellena después de que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="fd7d6-188">Si se pasa un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-188">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="fd7d6-189">Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los métodos de ciclo de vida del componente [`OnAfterRenderAsync` u `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="fd7d6-190">Cuando se trabaje con tipos genéricos y se devuelva un valor, use <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-190">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="fd7d6-191">Se llama al método `GenericMethod` directamente en el objeto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="fd7d6-192">En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="fd7d6-193">Referencia a elementos en componentes</span><span class="sxs-lookup"><span data-stu-id="fd7d6-193">Reference elements across components</span></span>

<span data-ttu-id="fd7d6-194">La validez de un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> solo está garantizada en el método de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> de un componente (y una referencia de elemento es un `struct`), por lo que una referencia de elemento no se puede pasar entre componentes.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-194">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="fd7d6-195">Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="fd7d6-196">Permitir que los componentes secundarios registren devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="fd7d6-197">Invoca las devoluciones de llamada registradas durante el evento <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> con la referencia de elemento que se ha pasado.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-197">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="fd7d6-198">Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="fd7d6-199">En el ejemplo siguiente Blazor WebAssembly se muestra este enfoque.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="fd7d6-200">En el elemento `<head>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-200">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="fd7d6-201">En el elemento `<body>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-201">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="fd7d6-202">`Pages/Index.razor` (componente primario):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-202">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="fd7d6-203">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-203">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="fd7d6-204">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-204">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="fd7d6-205">`Shared/SurveyPrompt.razor` (componente secundario):</span><span class="sxs-lookup"><span data-stu-id="fd7d6-205">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="fd7d6-206">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-206">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="fd7d6-207">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fd7d6-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="fd7d6-208">Protección de las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="fd7d6-208">Harden JS interop calls</span></span>

<span data-ttu-id="fd7d6-209">La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-209">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="fd7d6-210">De forma predeterminada, una aplicación Blazor Server agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-210">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="fd7d6-211">Si una aplicación puede tolerar un tiempo de espera más restrictivo, establézcalo recurriendo a uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-211">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="fd7d6-212">Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-212">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="fd7d6-213">Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-213">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="fd7d6-214">Para más información sobre el agotamiento de recursos, vea <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-214">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="fd7d6-215">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="fd7d6-215">Avoid circular object references</span></span>

<span data-ttu-id="fd7d6-216">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-216">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="fd7d6-217">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-217">.NET method calls.</span></span>
* <span data-ttu-id="fd7d6-218">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="fd7d6-218">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="fd7d6-219">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="fd7d6-219">For more information, see the following issues:</span></span>

* <span data-ttu-id="fd7d6-220">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="fd7d6-220">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="fd7d6-221">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="fd7d6-221">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd7d6-222">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="fd7d6-222">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="fd7d6-223">Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="fd7d6-223">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="fd7d6-224">[Realización de transferencias de datos grandes en aplicaciones Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="fd7d6-224">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
