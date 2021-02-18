---
title: Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5b28ad594567e7bfc87e15eed419bea520125654
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280298"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="948fc-103">Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="948fc-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="948fc-104">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="948fc-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="948fc-105">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="948fc-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="948fc-106">En este artículo se describe cómo invocar funciones de JavaScript desde .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-106">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="948fc-107">Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="948fc-107">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="948fc-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="948fc-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="948fc-109">Agregue archivos JS (etiquetas de `<script>`) antes de la etiqueta `</body>` de cierre en el archivo `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="948fc-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="948fc-110">Asegúrese de que los archivos JS con métodos de interoperabilidad de JS se incluyen antes que los archivos JS del marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="948fc-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="948fc-111">Para llamar a JavaScript desde .NET, use la abstracción <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="948fc-111">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="948fc-112">Para emitir llamadas de interoperabilidad de JS, inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-112">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="948fc-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> toma un identificador de la función de JavaScript que se quiere invocar junto con un número cualquiera de argumentos serializables con JSON.</span><span class="sxs-lookup"><span data-stu-id="948fc-113"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="948fc-114">El identificador de función es relativo al ámbito global (`window`).</span><span class="sxs-lookup"><span data-stu-id="948fc-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="948fc-115">Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="948fc-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="948fc-116">No es necesario registrar la función para poder llamarla.</span><span class="sxs-lookup"><span data-stu-id="948fc-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="948fc-117">El tipo de valor devuelto `T` también debe ser serializable con JSON.</span><span class="sxs-lookup"><span data-stu-id="948fc-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="948fc-118">`T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.</span><span class="sxs-lookup"><span data-stu-id="948fc-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="948fc-119">Se llama con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> a las funciones de JavaScript que devuelven una [promesa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="948fc-119">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="948fc-120">`InvokeAsync` desencapsula la promesa y devuelve el valor que esta espera.</span><span class="sxs-lookup"><span data-stu-id="948fc-120">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="948fc-121">En el caso de las aplicaciones Blazor Server que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial.</span><span class="sxs-lookup"><span data-stu-id="948fc-121">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="948fc-122">Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="948fc-122">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="948fc-123">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="948fc-123">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="948fc-124">El siguiente ejemplo se basa en [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-124">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="948fc-125">En el ejemplo se muestra cómo invocar una función de JavaScript desde un método de C# que descarga un requisito del código de desarrollador a una API de JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="948fc-125">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="948fc-126">La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.</span><span class="sxs-lookup"><span data-stu-id="948fc-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="948fc-127">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:</span><span class="sxs-lookup"><span data-stu-id="948fc-127">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="948fc-128">El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript (`.js`) con una referencia al archivo de script:</span><span class="sxs-lookup"><span data-stu-id="948fc-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="948fc-129">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="948fc-129">The following component:</span></span>

* <span data-ttu-id="948fc-130">Invoca la función de JavaScript `convertArray` mediante `JS` cuando se selecciona un botón de componente ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="948fc-130">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="948fc-131">Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena,</span><span class="sxs-lookup"><span data-stu-id="948fc-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="948fc-132">que se devuelve al componente para que la muestre.</span><span class="sxs-lookup"><span data-stu-id="948fc-132">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="948fc-133">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="948fc-133">IJSRuntime</span></span>

<span data-ttu-id="948fc-134">Para usar la abstracción <xref:Microsoft.JSInterop.IJSRuntime>, adopte cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="948fc-134">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="948fc-135">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente de Razor (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="948fc-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="948fc-136">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="948fc-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="948fc-137">Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="948fc-137">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="948fc-138">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en una clase (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="948fc-138">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="948fc-139">Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="948fc-139">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="948fc-140">Se llama a la función con `JS.InvokeAsync` y se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="948fc-140">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="948fc-141">Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="948fc-141">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="948fc-142">En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:</span><span class="sxs-lookup"><span data-stu-id="948fc-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="948fc-143">`showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="948fc-143">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="948fc-144">`displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="948fc-144">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="948fc-145">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="948fc-145">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="948fc-146">Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo `wwwroot/index.html` (Blazor WebAssembly) o en el archivo `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="948fc-146">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="948fc-147">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="948fc-147">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=23)]

<span data-ttu-id="948fc-148">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="948fc-148">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="948fc-149">No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="948fc-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="948fc-150">Los métodos de .NET interoperan con las funciones de JavaScript en el archivo `exampleJsInterop.js` llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="948fc-150">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="948fc-151">La abstracción <xref:Microsoft.JSInterop.IJSRuntime> es asincrónica para dar cabida a escenarios de servidor Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="948fc-151">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="948fc-152">Si la aplicación es una aplicación Blazor WebAssembly y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado <xref:Microsoft.JSInterop.IJSInProcessRuntime> y llame a <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> en su lugar.</span><span class="sxs-lookup"><span data-stu-id="948fc-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="948fc-153">Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="948fc-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="948fc-154">Para permitir el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar, vea la sección [Aislamiento de JavaScript y referencias a objetos en Blazor](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="948fc-154">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="948fc-155">La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="948fc-155">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="948fc-156">El componente:</span><span class="sxs-lookup"><span data-stu-id="948fc-156">The component:</span></span>

* <span data-ttu-id="948fc-157">Recibe la entrada del usuario a través de un mensaje de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-157">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="948fc-158">Devuelve el texto al componente para que lo procese.</span><span class="sxs-lookup"><span data-stu-id="948fc-158">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="948fc-159">Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="948fc-159">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="948fc-160">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="948fc-160">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="948fc-161">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="948fc-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="948fc-162">Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **`Trigger JavaScript Prompt`** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="948fc-162">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="948fc-163">La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="948fc-164">El componente almacena el nombre del usuario en una variable local, `name`.</span><span class="sxs-lookup"><span data-stu-id="948fc-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="948fc-165">La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.</span><span class="sxs-lookup"><span data-stu-id="948fc-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="948fc-166">Llamada a una función de JavaScript vacía</span><span class="sxs-lookup"><span data-stu-id="948fc-166">Call a void JavaScript function</span></span>

<span data-ttu-id="948fc-167">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="948fc-167">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="948fc-168">Funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="948fc-168">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="948fc-169">Si .NET no es necesario para leer el resultado de una llamada de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-169">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="948fc-170">Detección de cuándo se está obteniendo una representación previa de la aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="948fc-170">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="948fc-171">Captura de referencias a elementos</span><span class="sxs-lookup"><span data-stu-id="948fc-171">Capture references to elements</span></span>

<span data-ttu-id="948fc-172">Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="948fc-172">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="948fc-173">Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="948fc-173">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="948fc-174">Use el siguiente método para capturar referencias a elementos HTML en un componente:</span><span class="sxs-lookup"><span data-stu-id="948fc-174">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="948fc-175">Agregue un atributo `@ref` al elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="948fc-175">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="948fc-176">Defina un campo de tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cuyo nombre coincida con el valor del atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="948fc-176">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="948fc-177">En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:</span><span class="sxs-lookup"><span data-stu-id="948fc-177">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="948fc-178">Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor.</span><span class="sxs-lookup"><span data-stu-id="948fc-178">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="948fc-179">Este escenario es útil cuando una API de terceros proporciona contenido al elemento.</span><span class="sxs-lookup"><span data-stu-id="948fc-179">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="948fc-180">Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.</span><span class="sxs-lookup"><span data-stu-id="948fc-180">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="948fc-181">En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="948fc-181">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="948fc-182">Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.</span><span class="sxs-lookup"><span data-stu-id="948fc-182">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="948fc-183"><xref:Microsoft.AspNetCore.Components.ElementReference> se pasa al código de JavaScript por medio de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="948fc-183">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="948fc-184">El código de JavaScript recibe una instancia de `HTMLElement` que puede usar con las API de DOM habituales.</span><span class="sxs-lookup"><span data-stu-id="948fc-184">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="948fc-185">Por ejemplo, el siguiente código define un método de extensión de .NET que permite enviar un clic del mouse a un elemento:</span><span class="sxs-lookup"><span data-stu-id="948fc-185">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="948fc-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="948fc-186">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="948fc-187">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) en el código de C# para enfocar un elemento que está integrado en el marco Blazor y funciona con referencias de elemento.</span><span class="sxs-lookup"><span data-stu-id="948fc-187">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="948fc-188">Para llamar a una función de JavaScript que no devuelve un valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="948fc-188">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="948fc-189">El siguiente código desencadena un evento `Click` de cliente al llamar a la función de JavaScript anterior con el elemento <xref:Microsoft.AspNetCore.Components.ElementReference> capturado:</span><span class="sxs-lookup"><span data-stu-id="948fc-189">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="948fc-190">Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="948fc-190">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="948fc-191">Se llama al método `clickElement` directamente en el objeto.</span><span class="sxs-lookup"><span data-stu-id="948fc-191">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="948fc-192">En el siguiente ejemplo se da por hecho que el método `TriggerClickEvent` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="948fc-192">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="948fc-193">La variable `exampleButton` solo se rellena después de que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="948fc-193">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="948fc-194">Si se pasa un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="948fc-194">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="948fc-195">Para manipular referencias de elemento una vez finalizada la representación del componente, use los [métodos de ciclo de vida del componente `OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="948fc-195">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="948fc-196">Cuando se trabaje con tipos genéricos y se devuelva un valor, use <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="948fc-196">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="948fc-197">Se llama al método `GenericMethod` directamente en el objeto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="948fc-197">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="948fc-198">En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="948fc-198">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="948fc-199">Referencia a elementos en componentes</span><span class="sxs-lookup"><span data-stu-id="948fc-199">Reference elements across components</span></span>

<span data-ttu-id="948fc-200"><xref:Microsoft.AspNetCore.Components.ElementReference> no se puede pasar entre componentes porque:</span><span class="sxs-lookup"><span data-stu-id="948fc-200">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="948fc-201">Solo se garantiza que la instancia vaya a existir después de que se represente el componente, lo que es durante o después de la ejecución del método <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> del componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-201">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="948fc-202"><xref:Microsoft.AspNetCore.Components.ElementReference> es una [`struct`](/csharp/language-reference/builtin-types/struct), que no se puede pasar como [parámetro del componente](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="948fc-202">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="948fc-203">Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:</span><span class="sxs-lookup"><span data-stu-id="948fc-203">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="948fc-204">Permitir que los componentes secundarios registren devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="948fc-204">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="948fc-205">Invoca las devoluciones de llamada registradas durante el evento <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> con la referencia de elemento que se ha pasado.</span><span class="sxs-lookup"><span data-stu-id="948fc-205">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="948fc-206">Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="948fc-206">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="948fc-207">En el ejemplo siguiente Blazor WebAssembly se muestra este enfoque.</span><span class="sxs-lookup"><span data-stu-id="948fc-207">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="948fc-208">En el elemento `<head>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="948fc-208">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="948fc-209">En el elemento `<body>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="948fc-209">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="948fc-210">`Pages/Index.razor` (componente primario):</span><span class="sxs-lookup"><span data-stu-id="948fc-210">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="948fc-211">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="948fc-211">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="948fc-212">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="948fc-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="948fc-213">`Shared/SurveyPrompt.razor` (componente secundario):</span><span class="sxs-lookup"><span data-stu-id="948fc-213">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="948fc-214">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="948fc-214">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="948fc-215">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="948fc-215">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="948fc-216">Protección de las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="948fc-216">Harden JS interop calls</span></span>

<span data-ttu-id="948fc-217">La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable.</span><span class="sxs-lookup"><span data-stu-id="948fc-217">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="948fc-218">De forma predeterminada, una aplicación Blazor Server agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto.</span><span class="sxs-lookup"><span data-stu-id="948fc-218">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="948fc-219">Si una aplicación puede tolerar un tiempo de espera más restrictivo, establézcalo recurriendo a uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="948fc-219">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="948fc-220">Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="948fc-220">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="948fc-221">Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="948fc-221">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="948fc-222">Para más información sobre el agotamiento de recursos, vea <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="948fc-222">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="948fc-223">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="948fc-223">Avoid circular object references</span></span>

<span data-ttu-id="948fc-224">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="948fc-224">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="948fc-225">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-225">.NET method calls.</span></span>
* <span data-ttu-id="948fc-226">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="948fc-226">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="948fc-227">Para más información, consulte [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos).</span><span class="sxs-lookup"><span data-stu-id="948fc-227">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="948fc-228">Aislamiento de JavaScript y referencias a objetos en Blazor</span><span class="sxs-lookup"><span data-stu-id="948fc-228">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="948fc-229">Blazor permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar.</span><span class="sxs-lookup"><span data-stu-id="948fc-229">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="948fc-230">El aislamiento de JavaScript reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="948fc-230">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="948fc-231">El código JavaScript importado no contamina el espacio de nombres global.</span><span class="sxs-lookup"><span data-stu-id="948fc-231">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="948fc-232">No es necesario que los consumidores de una biblioteca y componentes importen el código JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="948fc-232">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="948fc-233">Por ejemplo, el siguiente módulo de JavaScript exporta una función de JavaScript para mostrar un mensaje del explorador:</span><span class="sxs-lookup"><span data-stu-id="948fc-233">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="948fc-234">Agregue el módulo de JavaScript anterior a una biblioteca de .NET como un recurso web estático (`wwwroot/exampleJsInterop.js`) y, después, importe dicho módulo al código .NET llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> en el servicio <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="948fc-234">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="948fc-235">El servicio se inserta como `js` (no se muestra) en el siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="948fc-235">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="948fc-236">El identificador de `import` del ejemplo anterior es un identificador especial que se usa específicamente para importar un módulo de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-236">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="948fc-237">Especifique el módulo por medio de su ruta de acceso de recurso web estático estable: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="948fc-237">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="948fc-238">El segmento de tazado del directorio actual (`./`) es necesario para crear la ruta de recurso estático correcta para el archivo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-238">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="948fc-239">La importación dinámica de un módulo requiere una solicitud de red, por lo que solo se puede lograr de forma asincrónica llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="948fc-239">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="948fc-240">El marcador de posición `{LIBRARY NAME}` es el nombre de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="948fc-240">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="948fc-241">El marcador de posición `{PATH UNDER WWWROOT}` es la ruta de acceso al script en `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="948fc-241">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="948fc-242"><xref:Microsoft.JSInterop.IJSRuntime> importa el módulo como un elemento `IJSObjectReference`, que es una referencia a un objeto de JavaScript hecha desde código .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-242"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="948fc-243">Use `IJSObjectReference` para invocar funciones de JavaScript exportadas desde el módulo:</span><span class="sxs-lookup"><span data-stu-id="948fc-243">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="948fc-244">`IJSInProcessObjectReference` representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="948fc-244">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="948fc-245">Uso de las bibliotecas de JavaScript que representan la interfaz de usuario (elementos DOM)</span><span class="sxs-lookup"><span data-stu-id="948fc-245">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="948fc-246">En ocasiones, es posible que quiera usar bibliotecas de JavaScript que generen elementos de la interfaz de usuario visibles en el DOM del explorador.</span><span class="sxs-lookup"><span data-stu-id="948fc-246">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="948fc-247">A primera vista, esto podría parecer difícil porque el sistema de comparación de Blazor se basa en tener control sobre el árbol de elementos DOM y encuentra errores si algún código externo muta el árbol DOM e invalida su mecanismo para aplicar las comparaciones.</span><span class="sxs-lookup"><span data-stu-id="948fc-247">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="948fc-248">No se trata de una limitación específica de Blazor.</span><span class="sxs-lookup"><span data-stu-id="948fc-248">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="948fc-249">El mismo desafío se produce con cualquier marco de interfaz de usuario basado en comparaciones.</span><span class="sxs-lookup"><span data-stu-id="948fc-249">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="948fc-250">Afortunadamente, es sencillo insertar una interfaz de usuario generada externamente dentro de una interfaz de usuario de componentes de Blazor de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="948fc-250">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="948fc-251">La técnica recomendada consiste en hacer que el código del componente (archivo `.razor`) produzca un elemento vacío.</span><span class="sxs-lookup"><span data-stu-id="948fc-251">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="948fc-252">En lo que se refiere al sistema de comparación de Blazor, el elemento siempre está vacío, por lo que el representador no recorre en el elemento y no interferirá con sus contenidos.</span><span class="sxs-lookup"><span data-stu-id="948fc-252">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="948fc-253">Esto hace que sea seguro rellenar el elemento con contenido arbitrario administrado externamente.</span><span class="sxs-lookup"><span data-stu-id="948fc-253">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="948fc-254">En el siguiente ejemplo se muestra el concepto.</span><span class="sxs-lookup"><span data-stu-id="948fc-254">The following example demonstrates the concept.</span></span> <span data-ttu-id="948fc-255">En la instrucción `if` en la que `firstRender` es `true`, haga algo con `myElement`.</span><span class="sxs-lookup"><span data-stu-id="948fc-255">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="948fc-256">Por ejemplo, llame a una biblioteca de JavaScript externa para rellenarla.</span><span class="sxs-lookup"><span data-stu-id="948fc-256">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="948fc-257">Blazor no hará nada con el contenido del elemento hasta que se quite este componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-257">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="948fc-258">Cuando se quite el componente, también se quitará el subárbol DOM completo del componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-258">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="948fc-259">Como ejemplo más detallado, considere el siguiente componente que representa un mapa interactivo mediante las [API de código abierto de Mapbox](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="948fc-259">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="948fc-260">El módulo de JavaScript correspondiente, que debe colocarse en `wwwroot/mapComponent.js`, es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="948fc-260">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="948fc-261">En el ejemplo anterior, reemplace la cadena `{ACCESS TOKEN}` con un token de acceso válido que puede obtener de https://account.mapbox.com.</span><span class="sxs-lookup"><span data-stu-id="948fc-261">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="948fc-262">Para generar el estilo correcto, agregue la siguiente etiqueta de hoja de estilos a la página HTML del host (`index.html` o `_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="948fc-262">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="948fc-263">En el ejemplo anterior se genera una interfaz de usuario de mapa interactiva, en la que el usuario:</span><span class="sxs-lookup"><span data-stu-id="948fc-263">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="948fc-264">Puede arrastrar para desplazarse o hacer zoom.</span><span class="sxs-lookup"><span data-stu-id="948fc-264">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="948fc-265">Hacer clic en los botones para saltar a ubicaciones predefinidas.</span><span class="sxs-lookup"><span data-stu-id="948fc-265">Click buttons to jump to predefined locations.</span></span>

![Mapa callejero de Mapbox de Tokio, Japón, con botones para seleccionar Bristol, Reino Unido, y Tokio, Japón](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="948fc-267">Los puntos clave que se deben comprender son:</span><span class="sxs-lookup"><span data-stu-id="948fc-267">The key points to understand are:</span></span>

 * <span data-ttu-id="948fc-268">En cuanto a Blazor, `<div>` con `@ref="mapElement"` se deja vacío.</span><span class="sxs-lookup"><span data-stu-id="948fc-268">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="948fc-269">Por lo tanto, es seguro que `mapbox-gl.js` la rellene y modifique su contenido con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="948fc-269">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="948fc-270">Puede usar esta técnica con cualquier biblioteca de JavaScript que represente la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="948fc-270">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="948fc-271">Incluso podría insertar componentes de un marco de JavaScript SPA de terceros dentro de los componentes de Blazor, siempre y cuando no intenten modificar otras partes de la página.</span><span class="sxs-lookup"><span data-stu-id="948fc-271">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="948fc-272">*No* es seguro que el código JavaScript externo modifique los elementos que Blazor no considere vacíos.</span><span class="sxs-lookup"><span data-stu-id="948fc-272">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="948fc-273">Al utilizar este enfoque, tenga en cuenta las reglas sobre cómo Blazor conserva o destruye los elementos DOM.</span><span class="sxs-lookup"><span data-stu-id="948fc-273">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="948fc-274">En el ejemplo anterior, el componente controla de forma segura los eventos de clic de botón y actualiza la instancia del mapa existente porque, de forma predeterminada, los elementos DOM se conservan siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="948fc-274">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="948fc-275">Si estuviera representando una lista de elementos de mapa desde dentro de un bucle `@foreach`, querría usar `@key` para garantizar la preservación de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-275">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="948fc-276">De lo contrario, los cambios en los datos de la lista podrían hacer que las instancias del componente conservaran el estado de las instancias anteriores de forma no deseada.</span><span class="sxs-lookup"><span data-stu-id="948fc-276">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="948fc-277">Para obtener más información, vea el artículo sobre el [uso de @key para conservar elementos y componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="948fc-277">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="948fc-278">Además, en el ejemplo anterior se muestra cómo es posible encapsular la lógica de JavaScript y las dependencias dentro de un módulo ES6, y cargarlo dinámicamente mediante el identificador `import`.</span><span class="sxs-lookup"><span data-stu-id="948fc-278">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="948fc-279">Para más información, consulte [Aislamiento de JavaScript y referencias a objetos](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="948fc-279">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="948fc-280">Límites de tamaño en las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="948fc-280">Size limits on JS interop calls</span></span>

<span data-ttu-id="948fc-281">En Blazor WebAssembly, el marco no impone límites en cuanto al tamaño de las entradas y salidas de las llamadas de interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="948fc-281">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="948fc-282">En Blazor Server, las llamadas de interoperabilidad de JS presentan un tamaño limitado por el tamaño máximo de los mensajes SignalR entrantes que se permite para los métodos del concentrador. Esto se aplica por medio de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (valor predeterminado: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="948fc-282">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="948fc-283">Los mensajes SignalR de JS a .NET de más de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> generan un error.</span><span class="sxs-lookup"><span data-stu-id="948fc-283">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="948fc-284">El marco no impone ningún límite de tamaño para un mensaje SignalR desde el concentrador a un cliente.</span><span class="sxs-lookup"><span data-stu-id="948fc-284">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="948fc-285">Para obtener más información, vea <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span><span class="sxs-lookup"><span data-stu-id="948fc-285">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="948fc-286">Módulos de JS</span><span class="sxs-lookup"><span data-stu-id="948fc-286">JS modules</span></span>

<span data-ttu-id="948fc-287">En el caso del aislamiento de JS, la interoperabilidad de JS funciona con la compatibilidad predeterminada del explorador para los [módulos EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificación de ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="948fc-287">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="948fc-288">Interoperabilidad de JS deserializada</span><span class="sxs-lookup"><span data-stu-id="948fc-288">Unmarshalled JS interop</span></span>

<span data-ttu-id="948fc-289">Los componentes de Blazor WebAssembly pueden experimentar un rendimiento deficiente cuando se serializan objetos .NET para la interoperabilidad de JS y se cumple cualquiera de las siguientes condiciones:</span><span class="sxs-lookup"><span data-stu-id="948fc-289">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="948fc-290">Un gran volumen de objetos .NET se serializan rápidamente.</span><span class="sxs-lookup"><span data-stu-id="948fc-290">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="948fc-291">Ejemplo: Las llamadas de interoperabilidad de JS se realizan en función del movimiento de un dispositivo de entrada, como girar una rueda del mouse.</span><span class="sxs-lookup"><span data-stu-id="948fc-291">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="948fc-292">Los objetos .NET grandes o muchos objetos .NET deben serializarse para la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="948fc-292">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="948fc-293">Ejemplo: Las llamadas de interoperabilidad de JS requieren la serialización de docenas de archivos.</span><span class="sxs-lookup"><span data-stu-id="948fc-293">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="948fc-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar sin la sobrecarga de serializar los datos de .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-294"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="948fc-295">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="948fc-295">In the following example:</span></span>

* <span data-ttu-id="948fc-296">Un [struct](/dotnet/csharp/language-reference/builtin-types/struct) que contiene una cadena y un entero se pasa sin serializar a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-296">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="948fc-297">Las funciones de JavaScript procesan los datos y devuelven un valor booleano o una cadena al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="948fc-297">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="948fc-298">Una cadena de JavaScript no se puede convertir directamente en un objeto `string` de .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-298">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="948fc-299">La función `unmarshalledFunctionReturnString` llama a `BINDING.js_string_to_mono_string` para administrar la conversión de una cadena de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-299">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="948fc-300">Los ejemplos siguientes no son casos de uso típicos para este escenario porque el [struct](/dotnet/csharp/language-reference/builtin-types/struct) pasado a JavaScript no da lugar a un rendimiento deficiente del componente.</span><span class="sxs-lookup"><span data-stu-id="948fc-300">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="948fc-301">En el ejemplo se usa un objeto pequeño simplemente para mostrar los conceptos para pasar datos de .NET no serializados.</span><span class="sxs-lookup"><span data-stu-id="948fc-301">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="948fc-302">Contenido de un bloque `<script>` en `wwwroot/index.html` o un archivo JavaScript externo al que hace referencia `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="948fc-302">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="948fc-303">El nombre, el comportamiento y la existencia de la función `js_string_to_mono_string` están sujetos a cambios en una versión futura de .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-303">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="948fc-304">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="948fc-304">For example:</span></span>
>
> * <span data-ttu-id="948fc-305">Es probable que se cambie el nombre de la función.</span><span class="sxs-lookup"><span data-stu-id="948fc-305">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="948fc-306">La propia función podría quitarse en favor de la conversión automática de cadenas por el marco.</span><span class="sxs-lookup"><span data-stu-id="948fc-306">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="948fc-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span><span class="sxs-lookup"><span data-stu-id="948fc-307">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="948fc-308">Si una instancia de `IJSUnmarshalledObjectReference` no se elimina en el código de C#, se puede eliminar en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="948fc-308">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="948fc-309">La siguiente función de `dispose` elimina la referencia de objeto cuando se llama desde JavaScript:</span><span class="sxs-lookup"><span data-stu-id="948fc-309">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="948fc-310">Los tipos de matriz se pueden convertir a partir de objetos JavaScript en objetos .NET mediante `js_typed_array_to_array`, pero la matriz de JavaScript debe ser una matriz con tipo.</span><span class="sxs-lookup"><span data-stu-id="948fc-310">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="948fc-311">Las matrices de JavaScript se pueden leer en código de C# como una matriz de objetos .NET (`object[]`).</span><span class="sxs-lookup"><span data-stu-id="948fc-311">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="948fc-312">Otros tipos de datos, como las matrices de cadenas, se pueden convertir pero requieren la creación de un nuevo objeto de matriz Mono (`mono_obj_array_new`) y el establecimiento de su valor (`mono_obj_array_set`).</span><span class="sxs-lookup"><span data-stu-id="948fc-312">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="948fc-313">Las funciones de JavaScript proporcionadas por el marco de Blazor, como `js_typed_array_to_array`, `mono_obj_array_new` y `mono_obj_array_set`, están sujetas a cambios de nombre, cambios de comportamiento o eliminación en versiones futuras de .NET.</span><span class="sxs-lookup"><span data-stu-id="948fc-313">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="948fc-314">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="948fc-314">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="948fc-315">Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="948fc-315">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
