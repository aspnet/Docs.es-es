---
title: Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre cómo invocar métodos de .NET desde funciones de JavaScript en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 5a00bfb87b8cfe0fb3e2a832a553b8a4cd45ee6d
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252505"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="38b98-103">Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="38b98-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="38b98-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="38b98-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="38b98-105">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="38b98-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="38b98-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="38b98-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="38b98-107">En este artículo se describe cómo invocar métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38b98-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="38b98-108">Para obtener información sobre cómo llamar a funciones de JavaScript desde .NET, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="38b98-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="38b98-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38b98-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="38b98-110">Llamada al método estático de .NET</span><span class="sxs-lookup"><span data-stu-id="38b98-110">Static .NET method call</span></span>

<span data-ttu-id="38b98-111">Para invocar un método de .NET estático desde JavaScript, use las funciones `DotNet.invokeMethod` o `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="38b98-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="38b98-112">Pase el identificador del método estático al que quiere llamar, el nombre del ensamblado que contiene la función, y los argumentos.</span><span class="sxs-lookup"><span data-stu-id="38b98-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="38b98-113">La versión asincrónica es preferible para admitir escenarios de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="38b98-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="38b98-114">El método de .NET debe ser público y estático y debe tener el atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="38b98-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="38b98-115">Actualmente no se admite la llamada a métodos genéricos abiertos.</span><span class="sxs-lookup"><span data-stu-id="38b98-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="38b98-116">La aplicación de ejemplo incluye un método de C# para devolver una matriz `int`.</span><span class="sxs-lookup"><span data-stu-id="38b98-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="38b98-117">El atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) se aplica al método.</span><span class="sxs-lookup"><span data-stu-id="38b98-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="38b98-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-118">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="38b98-119">El archivo JavaScript servido al cliente invoca el método de .NET de C#.</span><span class="sxs-lookup"><span data-stu-id="38b98-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="38b98-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="38b98-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="38b98-121">Al seleccionar el botón **`Trigger .NET static method ReturnArrayAsync`** , examine la salida de la consola en las herramientas de desarrollo web del explorador.</span><span class="sxs-lookup"><span data-stu-id="38b98-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="38b98-122">La salida de la consola es:</span><span class="sxs-lookup"><span data-stu-id="38b98-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="38b98-123">El cuarto valor de matriz se inserta en la matriz (`data.push(4);`) devuelta por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="38b98-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="38b98-124">De forma predeterminada, el identificador del método es su nombre, pero puede especificar un identificador diferente mediante el constructor de atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):</span><span class="sxs-lookup"><span data-stu-id="38b98-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="38b98-125">En el archivo JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="38b98-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="38b98-126">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="38b98-127">Llamada a métodos de instancia</span><span class="sxs-lookup"><span data-stu-id="38b98-127">Instance method call</span></span>

<span data-ttu-id="38b98-128">También puede llamar a métodos de instancia de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38b98-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="38b98-129">Para invocar un método de instancia de .NET desde JavaScript:</span><span class="sxs-lookup"><span data-stu-id="38b98-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="38b98-130">Pase la instancia de .NET por referencia a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="38b98-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="38b98-131">Realice una llamada estática a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="38b98-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="38b98-132">Encapsule la instancia de en una instancia <xref:Microsoft.JSInterop.DotNetObjectReference> y llame a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> en la instancia <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="38b98-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="38b98-133">Elimine los objetos <xref:Microsoft.JSInterop.DotNetObjectReference> (se incluye un ejemplo más adelante en esta sección).</span><span class="sxs-lookup"><span data-stu-id="38b98-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="38b98-134">Invoque métodos de instancia de .NET en la instancia mediante las funciones `invokeMethod` o `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="38b98-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="38b98-135">La instancia de .NET también se puede pasar como argumento al invocar otros métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38b98-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="38b98-136">La aplicación de ejemplo registra los mensajes en la consola del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="38b98-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="38b98-137">En los siguientes ejemplos de esta aplicación, examine la salida de la consola del explorador en las herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="38b98-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="38b98-138">Al seleccionar el botón **`Trigger .NET instance method HelloHelper.SayHello`** , se llama a `ExampleJsInterop.CallHelloHelperSayHello` y pasa un nombre, `Blazor`, al método.</span><span class="sxs-lookup"><span data-stu-id="38b98-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="38b98-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="38b98-140">`CallHelloHelperSayHello` invoca la función `sayHello` de JavaScript con una nueva instancia de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="38b98-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="38b98-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="38b98-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="38b98-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="38b98-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="38b98-143">El nombre se pasa al constructor de `HelloHelper`, que establece la propiedad `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="38b98-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="38b98-144">Cuando se ejecuta la función `sayHello` de JavaScript, `HelloHelper.SayHello` devuelve el mensaje `Hello, {Name}!`, y la función de JavaScript lo escribe en la consola.</span><span class="sxs-lookup"><span data-stu-id="38b98-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="38b98-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="38b98-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="38b98-146">Salida de la consola en las herramientas de desarrollo web del explorador:</span><span class="sxs-lookup"><span data-stu-id="38b98-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="38b98-147">Para evitar una fuga de memoria y permitir la recolección de elementos no utilizados en un componente que crea un objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, adopte uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="38b98-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="38b98-148">Elimine el objeto de la clase que ha creado la instancia de <xref:Microsoft.JSInterop.DotNetObjectReference>:</span><span class="sxs-lookup"><span data-stu-id="38b98-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="38b98-149">El patrón anterior que se muestra en la clase `ExampleJsInterop` también se puede implementar en un componente:</span><span class="sxs-lookup"><span data-stu-id="38b98-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="38b98-150">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="38b98-151">Si el componente o la clase no elimina el objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, elimínelo en el cliente mediante la llamada a `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="38b98-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="38b98-152">Llamada a métodos de instancia de componente</span><span class="sxs-lookup"><span data-stu-id="38b98-152">Component instance method call</span></span>

<span data-ttu-id="38b98-153">Para invocar los métodos de .NET de un componente:</span><span class="sxs-lookup"><span data-stu-id="38b98-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="38b98-154">Use la función `invokeMethod` o `invokeMethodAsync` para hacer una llamada de método estático al componente.</span><span class="sxs-lookup"><span data-stu-id="38b98-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="38b98-155">El método estático del componente encapsula la llamada a su método de instancia como un objeto <xref:System.Action> invocado.</span><span class="sxs-lookup"><span data-stu-id="38b98-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="38b98-156">Para las aplicaciones Blazor Server, donde es posible que varios usuarios utilicen simultáneamente el mismo componente, use una clase auxiliar para invocar métodos de instancia.</span><span class="sxs-lookup"><span data-stu-id="38b98-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="38b98-157">Para obtener más información, vea la sección [Clase auxiliar de método de instancia de componente](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="38b98-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="38b98-158">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="38b98-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="38b98-159">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="38b98-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-160">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="38b98-161">Para pasar argumentos al método de instancia:</span><span class="sxs-lookup"><span data-stu-id="38b98-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="38b98-162">Agregue parámetros a la invocación del método de JS.</span><span class="sxs-lookup"><span data-stu-id="38b98-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="38b98-163">En el ejemplo siguiente, se pasa un nombre al método.</span><span class="sxs-lookup"><span data-stu-id="38b98-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="38b98-164">Se pueden agregar parámetros adicionales a la lista según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="38b98-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="38b98-165">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="38b98-166">Proporcione los tipos correctos al objeto <xref:System.Action> para los parámetros.</span><span class="sxs-lookup"><span data-stu-id="38b98-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="38b98-167">Proporcione la lista de parámetros a los métodos de C#.</span><span class="sxs-lookup"><span data-stu-id="38b98-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="38b98-168">Invoque <xref:System.Action> (`UpdateMessage`) con los parámetros (`action.Invoke(name)`).</span><span class="sxs-lookup"><span data-stu-id="38b98-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="38b98-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="38b98-170">`message` de salida cuando se selecciona el botón **Call JS Method** (Llamar al método JS):</span><span class="sxs-lookup"><span data-stu-id="38b98-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="38b98-171">Clase auxiliar de método de instancia de componente</span><span class="sxs-lookup"><span data-stu-id="38b98-171">Component instance method helper class</span></span>

<span data-ttu-id="38b98-172">La clase auxiliar se usa para invocar un método de instancia como <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="38b98-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="38b98-173">Las clases auxiliares son útiles cuando:</span><span class="sxs-lookup"><span data-stu-id="38b98-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="38b98-174">Se representan varios componentes del mismo tipo en la misma página.</span><span class="sxs-lookup"><span data-stu-id="38b98-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="38b98-175">Se usa una aplicación Blazor Server, en la que es posible que varios usuarios utilicen un componente simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="38b98-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="38b98-176">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="38b98-176">In the following example:</span></span>

* <span data-ttu-id="38b98-177">El componente `JSInteropExample` contiene varios componentes `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="38b98-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="38b98-178">Cada componente `ListItem` consta de un mensaje y un botón.</span><span class="sxs-lookup"><span data-stu-id="38b98-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="38b98-179">Cuando se selecciona un botón de componente `ListItem`, el método `UpdateMessage` de ese objeto `ListItem` cambia el texto del elemento de lista y oculta el botón.</span><span class="sxs-lookup"><span data-stu-id="38b98-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="38b98-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="38b98-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="38b98-181">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="38b98-182">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="38b98-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="38b98-183">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="38b98-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="38b98-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-184">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="38b98-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="38b98-185">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="38b98-186">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="38b98-186">Avoid circular object references</span></span>

<span data-ttu-id="38b98-187">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="38b98-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="38b98-188">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="38b98-188">.NET method calls.</span></span>
* <span data-ttu-id="38b98-189">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="38b98-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="38b98-190">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="38b98-190">For more information, see the following issues:</span></span>

* <span data-ttu-id="38b98-191">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="38b98-191">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="38b98-192">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="38b98-192">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="38b98-193">Límites de tamaño en las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="38b98-193">Size limits on JS interop calls</span></span>

<span data-ttu-id="38b98-194">En Blazor WebAssembly, el marco no impone límites en cuanto al tamaño de las entradas y salidas de las llamadas de interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="38b98-194">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="38b98-195">En Blazor Server, las llamadas de interoperabilidad de JS presentan un tamaño limitado por el tamaño máximo de los mensajes SignalR entrantes que se permite para los métodos del concentrador. Esto se aplica por medio de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (valor predeterminado: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="38b98-195">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="38b98-196">Los mensajes SignalR de JS a .NET de más de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> generan un error.</span><span class="sxs-lookup"><span data-stu-id="38b98-196">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="38b98-197">El marco no impone ningún límite de tamaño para un mensaje SignalR desde el concentrador a un cliente.</span><span class="sxs-lookup"><span data-stu-id="38b98-197">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="38b98-198">Cuando el registro de SignalR no está establecido en [Depurar](xref:Microsoft.Extensions.Logging.LogLevel) o [Seguimiento](xref:Microsoft.Extensions.Logging.LogLevel), solo aparece un error relativo al tamaño del mensaje en la consola de herramientas de desarrollo del explorador:</span><span class="sxs-lookup"><span data-stu-id="38b98-198">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="38b98-199">Error: Conexión desconectada con el error "Error: El servidor ha devuelto un error al cerrarse: Conexión cerrada con un error.".</span><span class="sxs-lookup"><span data-stu-id="38b98-199">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="38b98-200">Cuando el [registro del lado servidor de SignalR](xref:signalr/diagnostics#server-side-logging) se establece en [Depurar](xref:Microsoft.Extensions.Logging.LogLevel) o [Seguimiento](xref:Microsoft.Extensions.Logging.LogLevel), el registro del lado servidor inicia una excepción <xref:System.IO.InvalidDataException> relativa a un error del tamaño del mensaje.</span><span class="sxs-lookup"><span data-stu-id="38b98-200">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="38b98-201">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="38b98-201">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="38b98-202">System.IO.InvalidDataException: Se ha superado el tamaño máximo del mensaje de 32768B.</span><span class="sxs-lookup"><span data-stu-id="38b98-202">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="38b98-203">El tamaño del mensaje se puede configurar en AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="38b98-203">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="38b98-204">Para aumentar el límite, establezca <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="38b98-204">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="38b98-205">En el ejemplo siguiente se establece el tamaño máximo del mensaje de recepción en 64 KB (64\*1024):</span><span class="sxs-lookup"><span data-stu-id="38b98-205">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="38b98-206">El aumento del límite de tamaño del mensaje entrante SignalR implica que se necesitan más recursos del servidor y lo expone a más riesgos por parte de un usuario malintencionado.</span><span class="sxs-lookup"><span data-stu-id="38b98-206">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="38b98-207">Además, la lectura de una gran cantidad de contenido en la memoria, como cadenas o matrices de bytes, también puede dar lugar a que las asignaciones funcionen de forma deficiente con el recolector de elementos no utilizados, lo que puede reducir significativamente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="38b98-207">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="38b98-208">Una opción para leer cargas grandes consiste en enviar el contenido en fragmentos más pequeños y procesar la carga como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="38b98-208">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="38b98-209">Se puede usar al leer cargas grandes de JSON o si los datos están disponibles en JavaScript como bytes sin formato.</span><span class="sxs-lookup"><span data-stu-id="38b98-209">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="38b98-210">Para obtener un ejemplo en el que se muestra el envío de cargas binarias de gran tamaño en Blazor Server que usa técnicas similares a las del componente `InputFile`, consulte la [aplicación de ejemplo para envíos binarios](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="38b98-210">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="38b98-211">Tenga en cuenta la guía siguiente al desarrollar código que transfiera un gran volumen de datos entre JavaScript y Blazor:</span><span class="sxs-lookup"><span data-stu-id="38b98-211">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="38b98-212">Segmente los datos en partes más pequeñas y envíe los segmentos de datos secuencialmente hasta que el servidor reciba todos los datos.</span><span class="sxs-lookup"><span data-stu-id="38b98-212">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="38b98-213">No asigne objetos grandes en código JavaScript y C#.</span><span class="sxs-lookup"><span data-stu-id="38b98-213">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="38b98-214">No bloquee el subproceso de interfaz de usuario principal durante períodos largos al enviar o recibir datos.</span><span class="sxs-lookup"><span data-stu-id="38b98-214">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="38b98-215">Libere la memoria consumida al completar o cancelar el proceso.</span><span class="sxs-lookup"><span data-stu-id="38b98-215">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="38b98-216">Aplique los requisitos adicionales siguientes por motivos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="38b98-216">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="38b98-217">Declare el tamaño máximo del archivo o los datos que se pueden pasar.</span><span class="sxs-lookup"><span data-stu-id="38b98-217">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="38b98-218">Declare la tasa mínima de carga desde el cliente al servidor.</span><span class="sxs-lookup"><span data-stu-id="38b98-218">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="38b98-219">Después de que el servidor reciba los datos, los datos se pueden:</span><span class="sxs-lookup"><span data-stu-id="38b98-219">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="38b98-220">Almacenar temporalmente en un búfer de memoria hasta que se recopilen todos los segmentos.</span><span class="sxs-lookup"><span data-stu-id="38b98-220">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="38b98-221">Consumir inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="38b98-221">Consumed immediately.</span></span> <span data-ttu-id="38b98-222">Por ejemplo, los datos se pueden almacenar inmediatamente en una base de datos o escribir en el disco a medida que se reciba cada segmento.</span><span class="sxs-lookup"><span data-stu-id="38b98-222">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="38b98-223">Módulos de JS</span><span class="sxs-lookup"><span data-stu-id="38b98-223">JS modules</span></span>

<span data-ttu-id="38b98-224">En el caso del aislamiento de JS, la interoperabilidad de JS funciona con la compatibilidad predeterminada del explorador para los [módulos EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificación de ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="38b98-224">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38b98-225">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="38b98-225">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="38b98-226">Ejemplo de `InteropComponent.razor` (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="38b98-226">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
