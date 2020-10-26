---
title: 'Llamada a funciones de JavaScript con métodos de .NET en :::no-loc(Blazor)::: de ASP.NET Core'
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 3bd881b124e00b91ab0aa9d3eb7531f10ef895f2
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326497"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="e4379-103">Llamada a funciones de JavaScript con métodos de .NET en :::no-loc(Blazor)::: de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4379-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="e4379-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e4379-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e4379-105">Una aplicación de :::no-loc(Blazor)::: puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="e4379-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="e4379-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS* ).</span><span class="sxs-lookup"><span data-stu-id="e4379-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="e4379-107">En este artículo se describe cómo invocar funciones de JavaScript desde .NET.</span><span class="sxs-lookup"><span data-stu-id="e4379-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="e4379-108">Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="e4379-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="e4379-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4379-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e4379-110">Para llamar a JavaScript desde .NET, use la abstracción <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="e4379-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="e4379-111">Para emitir llamadas de interoperabilidad de JS, inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente.</span><span class="sxs-lookup"><span data-stu-id="e4379-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="e4379-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> toma un identificador de la función de JavaScript que se quiere invocar junto con un número cualquiera de argumentos serializables con JSON.</span><span class="sxs-lookup"><span data-stu-id="e4379-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="e4379-113">El identificador de función es relativo al ámbito global (`window`).</span><span class="sxs-lookup"><span data-stu-id="e4379-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="e4379-114">Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="e4379-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="e4379-115">No es necesario registrar la función para poder llamarla.</span><span class="sxs-lookup"><span data-stu-id="e4379-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="e4379-116">El tipo de valor devuelto `T` también debe ser serializable con JSON.</span><span class="sxs-lookup"><span data-stu-id="e4379-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="e4379-117">`T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.</span><span class="sxs-lookup"><span data-stu-id="e4379-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="e4379-118">Se llama con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> a las funciones de JavaScript que devuelven una [promesa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="e4379-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="e4379-119">`InvokeAsync` desencapsula la promesa y devuelve el valor que esta espera.</span><span class="sxs-lookup"><span data-stu-id="e4379-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="e4379-120">En el caso de las aplicaciones :::no-loc(Blazor Server)::: que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial.</span><span class="sxs-lookup"><span data-stu-id="e4379-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="e4379-121">Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="e4379-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="e4379-122">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación :::no-loc(Blazor Server):::](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="e4379-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="e4379-123">El siguiente ejemplo se basa en [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e4379-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="e4379-124">En el ejemplo se muestra cómo invocar una función de JavaScript desde un método de C# que descarga un requisito del código de desarrollador a una API de JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="e4379-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="e4379-125">La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.</span><span class="sxs-lookup"><span data-stu-id="e4379-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="e4379-126">Dentro del elemento `<head>` de `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) o `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:</span><span class="sxs-lookup"><span data-stu-id="e4379-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="e4379-127">El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript (`.js`) con una referencia al archivo de script:</span><span class="sxs-lookup"><span data-stu-id="e4379-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="e4379-128">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="e4379-128">The following component:</span></span>

* <span data-ttu-id="e4379-129">Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="e4379-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="e4379-130">Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena,</span><span class="sxs-lookup"><span data-stu-id="e4379-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="e4379-131">que se devuelve al componente para que la muestre.</span><span class="sxs-lookup"><span data-stu-id="e4379-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="e4379-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="e4379-132">IJSRuntime</span></span>

<span data-ttu-id="e4379-133">Para usar la abstracción <xref:Microsoft.JSInterop.IJSRuntime>, adopte cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="e4379-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="e4379-134">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente de :::no-loc(Razor)::: (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="e4379-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="e4379-135">Dentro del elemento `<head>` de `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) o `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="e4379-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="e4379-136">Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="e4379-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="e4379-137">Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en una clase (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="e4379-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="e4379-138">Dentro del elemento `<head>` de `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) o `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), proporcione una función de JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="e4379-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="e4379-139">Se llama a la función con `JSRuntime.InvokeAsync` y se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="e4379-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="e4379-140">Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="e4379-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="e4379-141">En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:</span><span class="sxs-lookup"><span data-stu-id="e4379-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="e4379-142">`showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="e4379-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="e4379-143">`displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="e4379-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="e4379-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e4379-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="e4379-145">Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) o en el archivo `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::).</span><span class="sxs-lookup"><span data-stu-id="e4379-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="e4379-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="e4379-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="e4379-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="e4379-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/3.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="e4379-148">No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="e4379-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="e4379-149">Los métodos de .NET interoperan con las funciones de JavaScript en el archivo `exampleJsInterop.js` llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="e4379-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="e4379-150">La abstracción <xref:Microsoft.JSInterop.IJSRuntime> es asincrónica para dar cabida a escenarios de servidor :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="e4379-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="e4379-151">Si la aplicación es una aplicación :::no-loc(Blazor WebAssembly)::: y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado <xref:Microsoft.JSInterop.IJSInProcessRuntime> y llame a <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> en su lugar.</span><span class="sxs-lookup"><span data-stu-id="e4379-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="e4379-152">Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="e4379-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="e4379-153">Para permitir el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar, vea la sección [Aislamiento de JavaScript y referencias a objetos en :::no-loc(Blazor):::](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="e4379-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="e4379-154">La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="e4379-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="e4379-155">El componente:</span><span class="sxs-lookup"><span data-stu-id="e4379-155">The component:</span></span>

* <span data-ttu-id="e4379-156">Recibe la entrada del usuario a través de un mensaje de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e4379-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="e4379-157">Devuelve el texto al componente para que lo procese.</span><span class="sxs-lookup"><span data-stu-id="e4379-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="e4379-158">Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="e4379-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="e4379-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="e4379-159">`Pages/JsInterop.razor`:</span></span>

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
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="e4379-160">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="e4379-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="e4379-161">Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **`Trigger JavaScript Prompt`** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="e4379-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="e4379-162">La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente.</span><span class="sxs-lookup"><span data-stu-id="e4379-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="e4379-163">El componente almacena el nombre del usuario en una variable local, `name`.</span><span class="sxs-lookup"><span data-stu-id="e4379-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="e4379-164">La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.</span><span class="sxs-lookup"><span data-stu-id="e4379-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="e4379-165">Llamada a una función de JavaScript vacía</span><span class="sxs-lookup"><span data-stu-id="e4379-165">Call a void JavaScript function</span></span>

<span data-ttu-id="e4379-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="e4379-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="e4379-167">Funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="e4379-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="e4379-168">Si .NET no es necesario para leer el resultado de una llamada de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e4379-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="e4379-169">Detección de cuándo se está obteniendo una representación previa de la aplicación :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="e4379-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="e4379-170">Captura de referencias a elementos</span><span class="sxs-lookup"><span data-stu-id="e4379-170">Capture references to elements</span></span>

<span data-ttu-id="e4379-171">Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="e4379-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="e4379-172">Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="e4379-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="e4379-173">Use el siguiente método para capturar referencias a elementos HTML en un componente:</span><span class="sxs-lookup"><span data-stu-id="e4379-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="e4379-174">Agregue un atributo `@ref` al elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="e4379-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="e4379-175">Defina un campo de tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cuyo nombre coincida con el valor del atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="e4379-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="e4379-176">En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:</span><span class="sxs-lookup"><span data-stu-id="e4379-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="e4379-177">Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="e4379-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="e4379-178">Este escenario es útil cuando una API de terceros proporciona contenido al elemento.</span><span class="sxs-lookup"><span data-stu-id="e4379-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="e4379-179">Dado que :::no-loc(Blazor)::: no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de :::no-loc(Blazor)::: del elemento y el DOM.</span><span class="sxs-lookup"><span data-stu-id="e4379-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="e4379-180">En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque :::no-loc(Blazor)::: interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="e4379-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="e4379-181">Si la interoperabilidad de JS muta el contenido del elemento `MyList` y :::no-loc(Blazor)::: intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.</span><span class="sxs-lookup"><span data-stu-id="e4379-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="e4379-182">En lo que respecta al código .NET, un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> es un identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="e4379-182">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="e4379-183">*Lo único* que se puede hacer con <xref:Microsoft.AspNetCore.Components.ElementReference> es pasarlo a código de JavaScript a través de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="e4379-183">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="e4379-184">Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.</span><span class="sxs-lookup"><span data-stu-id="e4379-184">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="e4379-185">Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:</span><span class="sxs-lookup"><span data-stu-id="e4379-185">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="e4379-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="e4379-186">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="e4379-187">Para llamar a una función de JavaScript que no devuelve un valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="e4379-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e4379-188">El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento <xref:Microsoft.AspNetCore.Components.ElementReference> capturado:</span><span class="sxs-lookup"><span data-stu-id="e4379-188">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="e4379-189">Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="e4379-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="e4379-190">Se llama al método `Focus` directamente en el objeto.</span><span class="sxs-lookup"><span data-stu-id="e4379-190">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="e4379-191">En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="e4379-191">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="e4379-192">La variable `username` solo se rellena después de que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="e4379-192">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="e4379-193">Si se pasa un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="e4379-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="e4379-194">Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los métodos de ciclo de vida del componente [`OnAfterRenderAsync` u `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="e4379-194">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="e4379-195">Cuando se trabaje con tipos genéricos y se devuelva un valor, use <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="e4379-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="e4379-196">Se llama al método `GenericMethod` directamente en el objeto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="e4379-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="e4379-197">En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="e4379-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="e4379-198">Referencia a elementos en componentes</span><span class="sxs-lookup"><span data-stu-id="e4379-198">Reference elements across components</span></span>

<span data-ttu-id="e4379-199">La validez de una instancia <xref:Microsoft.AspNetCore.Components.ElementReference> solo está garantizada en el método <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> de un componente, cuando una referencia de elemento es un `struct`, por lo que una referencia de elemento no se puede pasar entre componentes.</span><span class="sxs-lookup"><span data-stu-id="e4379-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> instance is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span> <span data-ttu-id="e4379-200">Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:</span><span class="sxs-lookup"><span data-stu-id="e4379-200">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="e4379-201">Permitir que los componentes secundarios registren devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="e4379-201">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="e4379-202">Invoca las devoluciones de llamada registradas durante el evento <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> con la referencia de elemento que se ha pasado.</span><span class="sxs-lookup"><span data-stu-id="e4379-202">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="e4379-203">Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="e4379-203">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="e4379-204">En el ejemplo siguiente :::no-loc(Blazor WebAssembly)::: se muestra este enfoque.</span><span class="sxs-lookup"><span data-stu-id="e4379-204">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="e4379-205">En el elemento `<head>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="e4379-205">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="e4379-206">En el elemento `<body>` de `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="e4379-206">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="e4379-207">`Pages/Index.razor` (componente primario):</span><span class="sxs-lookup"><span data-stu-id="e4379-207">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="e4379-208">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="e4379-208">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="e4379-209">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="e4379-209">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="e4379-210">`Shared/SurveyPrompt.razor` (componente secundario):</span><span class="sxs-lookup"><span data-stu-id="e4379-210">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="e4379-211">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="e4379-211">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="e4379-212">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="e4379-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="e4379-213">Protección de las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="e4379-213">Harden JS interop calls</span></span>

<span data-ttu-id="e4379-214">La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable.</span><span class="sxs-lookup"><span data-stu-id="e4379-214">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="e4379-215">De forma predeterminada, una aplicación :::no-loc(Blazor Server)::: agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto.</span><span class="sxs-lookup"><span data-stu-id="e4379-215">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="e4379-216">Si una aplicación puede tolerar un tiempo de espera más restrictivo, establézcalo recurriendo a uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="e4379-216">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="e4379-217">Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="e4379-217">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="e4379-218">Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="e4379-218">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="e4379-219">Para más información sobre el agotamiento de recursos, vea <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="e4379-219">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="e4379-220">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="e4379-220">Avoid circular object references</span></span>

<span data-ttu-id="e4379-221">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="e4379-221">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="e4379-222">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="e4379-222">.NET method calls.</span></span>
* <span data-ttu-id="e4379-223">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="e4379-223">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="e4379-224">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="e4379-224">For more information, see the following issues:</span></span>

* <span data-ttu-id="e4379-225">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="e4379-225">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="e4379-226">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="e4379-226">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="e4379-227">Aislamiento de JavaScript y referencias a objetos en :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e4379-227">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="e4379-228">:::no-loc(Blazor)::: permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar.</span><span class="sxs-lookup"><span data-stu-id="e4379-228">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="e4379-229">El aislamiento de JavaScript reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="e4379-229">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="e4379-230">El código JavaScript importado no contamina el espacio de nombres global.</span><span class="sxs-lookup"><span data-stu-id="e4379-230">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="e4379-231">No es necesario que los consumidores de una biblioteca y componentes importen el código JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="e4379-231">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="e4379-232">Por ejemplo, el siguiente módulo de JavaScript exporta una función de JavaScript para mostrar un mensaje del explorador:</span><span class="sxs-lookup"><span data-stu-id="e4379-232">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="e4379-233">Agregue el módulo de JavaScript anterior a una biblioteca de .NET como un recurso web estático (`wwwroot/exampleJsInterop.js`) y, después, importe dicho módulo al código .NET usando el servicio <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="e4379-233">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="e4379-234">El servicio se inserta como `jsRuntime` (no se muestra) en el siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e4379-234">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="e4379-235">El identificador de `import` del ejemplo anterior es un identificador especial que se usa específicamente para importar un módulo de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e4379-235">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="e4379-236">Especifique el módulo por medio de su ruta de acceso de recurso web estático estable: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="e4379-236">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="e4379-237">El marcador de posición `{LIBRARY NAME}` es el nombre de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e4379-237">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="e4379-238">El marcador de posición `{PATH UNDER WWWROOT}` es la ruta de acceso al script en `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="e4379-238">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="e4379-239"><xref:Microsoft.JSInterop.IJSRuntime> importa el módulo como un elemento `IJSObjectReference`, que es una referencia a un objeto de JavaScript hecha desde código .NET.</span><span class="sxs-lookup"><span data-stu-id="e4379-239"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="e4379-240">Use `IJSObjectReference` para invocar funciones de JavaScript exportadas desde el módulo:</span><span class="sxs-lookup"><span data-stu-id="e4379-240">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="e4379-241">`IJSInProcessObjectReference` representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="e4379-241">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="e4379-242">`IJSUnmarshalledObjectReference` representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar sin la sobrecarga de serializar los datos de .NET.</span><span class="sxs-lookup"><span data-stu-id="e4379-242">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="e4379-243">Se puede usar en :::no-loc(Blazor WebAssembly)::: cuando el rendimiento es crucial:</span><span class="sxs-lookup"><span data-stu-id="e4379-243">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="e4379-244">Uso de las bibliotecas de JavaScript que representan la interfaz de usuario (elementos DOM)</span><span class="sxs-lookup"><span data-stu-id="e4379-244">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="e4379-245">En ocasiones, es posible que quiera usar bibliotecas de JavaScript que generen elementos de la interfaz de usuario visibles en el DOM del explorador.</span><span class="sxs-lookup"><span data-stu-id="e4379-245">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="e4379-246">A primera vista, esto podría parecer difícil porque el sistema de comparación de :::no-loc(Blazor)::: se basa en tener control sobre el árbol de elementos DOM y encuentra errores si algún código externo muta el árbol DOM e invalida su mecanismo para aplicar las comparaciones.</span><span class="sxs-lookup"><span data-stu-id="e4379-246">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="e4379-247">No se trata de una limitación específica de :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="e4379-247">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="e4379-248">El mismo desafío se produce con cualquier marco de interfaz de usuario basado en comparaciones.</span><span class="sxs-lookup"><span data-stu-id="e4379-248">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="e4379-249">Afortunadamente, es sencillo insertar una interfaz de usuario generada externamente dentro de una interfaz de usuario de componentes de :::no-loc(Blazor)::: de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="e4379-249">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="e4379-250">La técnica recomendada consiste en hacer que el código del componente (archivo `.razor`) produzca un elemento vacío.</span><span class="sxs-lookup"><span data-stu-id="e4379-250">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="e4379-251">En lo que se refiere al sistema de comparación de :::no-loc(Blazor):::, el elemento siempre está vacío, por lo que el representador no recorre en el elemento y no interferirá con sus contenidos.</span><span class="sxs-lookup"><span data-stu-id="e4379-251">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="e4379-252">Esto hace que sea seguro rellenar el elemento con contenido arbitrario administrado externamente.</span><span class="sxs-lookup"><span data-stu-id="e4379-252">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="e4379-253">En el siguiente ejemplo se muestra el concepto.</span><span class="sxs-lookup"><span data-stu-id="e4379-253">The following example demonstrates the concept.</span></span> <span data-ttu-id="e4379-254">En la instrucción `if` en la que `firstRender` es `true`, haga algo con `myElement`.</span><span class="sxs-lookup"><span data-stu-id="e4379-254">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="e4379-255">Por ejemplo, llame a una biblioteca de JavaScript externa para rellenarla.</span><span class="sxs-lookup"><span data-stu-id="e4379-255">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="e4379-256">:::no-loc(Blazor)::: no hará nada con el contenido del elemento hasta que se quite este componente.</span><span class="sxs-lookup"><span data-stu-id="e4379-256">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="e4379-257">Cuando se quite el componente, también se quitará el subárbol DOM completo del componente.</span><span class="sxs-lookup"><span data-stu-id="e4379-257">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

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

<span data-ttu-id="e4379-258">Como ejemplo más detallado, considere el siguiente componente que representa un mapa interactivo mediante las [API de código abierto de Mapbox](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="e4379-258">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="e4379-259">El módulo de JavaScript correspondiente, que debe colocarse en `wwwroot/mapComponent.js`, es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="e4379-259">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="e4379-260">En el ejemplo anterior, reemplace la cadena `{ACCESS TOKEN}` con un token de acceso válido que puede obtener de https://account.mapbox.com.</span><span class="sxs-lookup"><span data-stu-id="e4379-260">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="e4379-261">Para generar el estilo correcto, agregue la siguiente etiqueta de hoja de estilos a la página HTML del host (`index.html` o `_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="e4379-261">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="e4379-262">En el ejemplo anterior se genera una interfaz de usuario de mapa interactiva, en la que el usuario:</span><span class="sxs-lookup"><span data-stu-id="e4379-262">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="e4379-263">Puede arrastrar para desplazarse o hacer zoom.</span><span class="sxs-lookup"><span data-stu-id="e4379-263">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="e4379-264">Hacer clic en los botones para saltar a ubicaciones predefinidas.</span><span class="sxs-lookup"><span data-stu-id="e4379-264">Click buttons to jump to predefined locations.</span></span>

![Mapa callejero de Mapbox de Tokio, Japón, con botones para seleccionar Bristol, Reino Unido, y Tokio, Japón](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="e4379-266">Los puntos clave que se deben comprender son:</span><span class="sxs-lookup"><span data-stu-id="e4379-266">The key points to understand are:</span></span>

 * <span data-ttu-id="e4379-267">En cuanto a :::no-loc(Blazor):::, `<div>` con `@ref="mapElement"` se deja vacío.</span><span class="sxs-lookup"><span data-stu-id="e4379-267">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="e4379-268">Por lo tanto, es seguro que `mapbox-gl.js` la rellene y modifique su contenido con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="e4379-268">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="e4379-269">Puede usar esta técnica con cualquier biblioteca de JavaScript que represente la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="e4379-269">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="e4379-270">Incluso podría insertar componentes de un marco de JavaScript SPA de terceros dentro de los componentes de :::no-loc(Blazor):::, siempre y cuando no intenten modificar otras partes de la página.</span><span class="sxs-lookup"><span data-stu-id="e4379-270">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="e4379-271">*No* es seguro que el código JavaScript externo modifique los elementos que :::no-loc(Blazor)::: no considere vacíos.</span><span class="sxs-lookup"><span data-stu-id="e4379-271">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="e4379-272">Al utilizar este enfoque, tenga en cuenta las reglas sobre cómo :::no-loc(Blazor)::: conserva o destruye los elementos DOM.</span><span class="sxs-lookup"><span data-stu-id="e4379-272">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="e4379-273">En el ejemplo anterior, el componente controla de forma segura los eventos de clic de botón y actualiza la instancia del mapa existente porque, de forma predeterminada, los elementos DOM se conservan siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="e4379-273">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="e4379-274">Si estuviera representando una lista de elementos de mapa desde dentro de un bucle `@foreach`, querría usar `@key` para garantizar la preservación de las instancias del componente.</span><span class="sxs-lookup"><span data-stu-id="e4379-274">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="e4379-275">De lo contrario, los cambios en los datos de la lista podrían hacer que las instancias del componente conservaran el estado de las instancias anteriores de forma no deseada.</span><span class="sxs-lookup"><span data-stu-id="e4379-275">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="e4379-276">Para obtener más información, vea el artículo sobre el [uso de @key para conservar elementos y componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="e4379-276">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="e4379-277">Además, en el ejemplo anterior se muestra cómo es posible encapsular la lógica de JavaScript y las dependencias dentro de un módulo ES6, y cargarlo dinámicamente mediante el identificador `import`.</span><span class="sxs-lookup"><span data-stu-id="e4379-277">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="e4379-278">Para más información, consulte [Aislamiento de JavaScript y referencias a objetos](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="e4379-278">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="e4379-279">Límites de tamaño en las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="e4379-279">Size limits on JS interop calls</span></span>

<span data-ttu-id="e4379-280">En :::no-loc(Blazor WebAssembly):::, la plataforma no impone límites en cuanto al tamaño de las entradas y salidas de las llamadas de interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="e4379-280">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="e4379-281">En :::no-loc(Blazor Server):::, el resultado de una llamada de interoperabilidad de JS está limitado por el tamaño de carga máximo aplicado por :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), cuyo valor predeterminado es 32 KB.</span><span class="sxs-lookup"><span data-stu-id="e4379-281">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="e4379-282">Las aplicaciones que intentan responder a una llamada de interoperabilidad de JS con una carga mayor que <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> producen un error.</span><span class="sxs-lookup"><span data-stu-id="e4379-282">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="e4379-283">Se puede configurar un límite mayor mediante la modificación de <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span><span class="sxs-lookup"><span data-stu-id="e4379-283">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="e4379-284">En el ejemplo siguiente se establece el tamaño máximo del mensaje de recepción en 64 KB (64*1024*1024):</span><span class="sxs-lookup"><span data-stu-id="e4379-284">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="e4379-285">Aumentar el límite de :::no-loc(SignalR)::: implica el uso de más recursos del servidor y lo expone a más riesgos por parte de un usuario malintencionado.</span><span class="sxs-lookup"><span data-stu-id="e4379-285">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="e4379-286">Además, la lectura de una gran cantidad de contenido en la memoria, como cadenas o matrices de bytes, también puede dar lugar a que las asignaciones funcionen de forma deficiente con el recolector de elementos no utilizados, lo que puede reducir significativamente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="e4379-286">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="e4379-287">Una opción para leer grandes cargas es considerar la posibilidad de enviar el contenido en fragmentos más pequeños y procesar la carga como una clase <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="e4379-287">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e4379-288">Se puede usar al leer cargas grandes de JSON o si los datos están disponibles en JavaScript como bytes sin formato.</span><span class="sxs-lookup"><span data-stu-id="e4379-288">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="e4379-289">Para obtener un ejemplo en el que se muestra el envío de cargas binarias de gran tamaño en :::no-loc(Blazor Server)::: que usa técnicas similares a las del componente `InputFile`, consulte la [aplicación de ejemplo para envíos binarios](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="e4379-289">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="e4379-290">Tenga en cuenta la guía siguiente al desarrollar código que transfiera un gran volumen de datos entre JavaScript y :::no-loc(Blazor)::::</span><span class="sxs-lookup"><span data-stu-id="e4379-290">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="e4379-291">Segmente los datos en partes más pequeñas y envíe los segmentos de datos secuencialmente hasta que el servidor reciba todos los datos.</span><span class="sxs-lookup"><span data-stu-id="e4379-291">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="e4379-292">No asigne objetos grandes en código JavaScript y C#.</span><span class="sxs-lookup"><span data-stu-id="e4379-292">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="e4379-293">No bloquee el subproceso de interfaz de usuario principal durante períodos largos al enviar o recibir datos.</span><span class="sxs-lookup"><span data-stu-id="e4379-293">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="e4379-294">Libere la memoria consumida al completar o cancelar el proceso.</span><span class="sxs-lookup"><span data-stu-id="e4379-294">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="e4379-295">Aplique los requisitos adicionales siguientes por motivos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="e4379-295">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="e4379-296">Declare el tamaño máximo del archivo o los datos que se pueden pasar.</span><span class="sxs-lookup"><span data-stu-id="e4379-296">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="e4379-297">Declare la tasa mínima de carga desde el cliente al servidor.</span><span class="sxs-lookup"><span data-stu-id="e4379-297">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="e4379-298">Después de que el servidor reciba los datos, los datos se pueden:</span><span class="sxs-lookup"><span data-stu-id="e4379-298">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="e4379-299">Almacenar temporalmente en un búfer de memoria hasta que se recopilen todos los segmentos.</span><span class="sxs-lookup"><span data-stu-id="e4379-299">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="e4379-300">Consumir inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="e4379-300">Consumed immediately.</span></span> <span data-ttu-id="e4379-301">Por ejemplo, los datos se pueden almacenar inmediatamente en una base de datos o escribir en el disco a medida que se reciba cada segmento.</span><span class="sxs-lookup"><span data-stu-id="e4379-301">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4379-302">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e4379-302">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="e4379-303">Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="e4379-303">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
