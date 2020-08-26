---
title: Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre cómo invocar métodos de .NET desde funciones de JavaScript en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
ms.openlocfilehash: 3df0fafe85d6decac3be41d4e25a4db51d8d72d8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627057"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="b3527-103">Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b3527-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="b3527-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b3527-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b3527-105">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="b3527-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="b3527-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="b3527-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="b3527-107">En este artículo se describe cómo invocar métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3527-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="b3527-108">Para obtener información sobre cómo llamar a funciones de JavaScript desde .NET, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="b3527-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="b3527-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b3527-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="b3527-110">Llamada al método estático de .NET</span><span class="sxs-lookup"><span data-stu-id="b3527-110">Static .NET method call</span></span>

<span data-ttu-id="b3527-111">Para invocar un método de .NET estático desde JavaScript, use las funciones `DotNet.invokeMethod` o `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="b3527-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="b3527-112">Pase el identificador del método estático al que quiere llamar, el nombre del ensamblado que contiene la función, y los argumentos.</span><span class="sxs-lookup"><span data-stu-id="b3527-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="b3527-113">La versión asincrónica es preferible para admitir escenarios de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b3527-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="b3527-114">El método de .NET debe ser público y estático y debe tener el atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="b3527-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="b3527-115">Actualmente no se admite la llamada a métodos genéricos abiertos.</span><span class="sxs-lookup"><span data-stu-id="b3527-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="b3527-116">La aplicación de ejemplo incluye un método de C# para devolver una matriz `int`.</span><span class="sxs-lookup"><span data-stu-id="b3527-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="b3527-117">El atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) se aplica al método.</span><span class="sxs-lookup"><span data-stu-id="b3527-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="b3527-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="b3527-119">El archivo JavaScript servido al cliente invoca el método de .NET de C#.</span><span class="sxs-lookup"><span data-stu-id="b3527-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="b3527-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b3527-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="b3527-121">Al seleccionar el botón **`Trigger .NET static method ReturnArrayAsync`** , examine la salida de la consola en las herramientas de desarrollo web del explorador.</span><span class="sxs-lookup"><span data-stu-id="b3527-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="b3527-122">La salida de la consola es:</span><span class="sxs-lookup"><span data-stu-id="b3527-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="b3527-123">El cuarto valor de matriz se inserta en la matriz (`data.push(4);`) devuelta por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="b3527-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="b3527-124">De forma predeterminada, el identificador del método es su nombre, pero puede especificar un identificador diferente mediante el constructor de atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):</span><span class="sxs-lookup"><span data-stu-id="b3527-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="b3527-125">En el archivo JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="b3527-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="b3527-126">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b3527-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="b3527-127">Llamada a métodos de instancia</span><span class="sxs-lookup"><span data-stu-id="b3527-127">Instance method call</span></span>

<span data-ttu-id="b3527-128">También puede llamar a métodos de instancia de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3527-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="b3527-129">Para invocar un método de instancia de .NET desde JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b3527-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="b3527-130">Pase la instancia de .NET por referencia a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b3527-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="b3527-131">Realice una llamada estática a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="b3527-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b3527-132">Encapsule la instancia de en una instancia <xref:Microsoft.JSInterop.DotNetObjectReference> y llame a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> en la instancia <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="b3527-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="b3527-133">Elimine los objetos <xref:Microsoft.JSInterop.DotNetObjectReference> (se incluye un ejemplo más adelante en esta sección).</span><span class="sxs-lookup"><span data-stu-id="b3527-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="b3527-134">Invoque métodos de instancia de .NET en la instancia mediante las funciones `invokeMethod` o `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="b3527-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="b3527-135">La instancia de .NET también se puede pasar como argumento al invocar otros métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3527-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="b3527-136">La aplicación de ejemplo registra los mensajes en la consola del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="b3527-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="b3527-137">En los siguientes ejemplos de esta aplicación, examine la salida de la consola del explorador en las herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="b3527-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="b3527-138">Al seleccionar el botón **`Trigger .NET instance method HelloHelper.SayHello`** , se llama a `ExampleJsInterop.CallHelloHelperSayHello` y pasa un nombre, `Blazor`, al método.</span><span class="sxs-lookup"><span data-stu-id="b3527-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="b3527-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="b3527-140">`CallHelloHelperSayHello` invoca la función `sayHello` de JavaScript con una nueva instancia de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="b3527-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="b3527-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="b3527-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="b3527-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b3527-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="b3527-143">El nombre se pasa al constructor de `HelloHelper`, que establece la propiedad `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="b3527-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="b3527-144">Cuando se ejecuta la función `sayHello` de JavaScript, `HelloHelper.SayHello` devuelve el mensaje `Hello, {Name}!`, y la función de JavaScript lo escribe en la consola.</span><span class="sxs-lookup"><span data-stu-id="b3527-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="b3527-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="b3527-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="b3527-146">Salida de la consola en las herramientas de desarrollo web del explorador:</span><span class="sxs-lookup"><span data-stu-id="b3527-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="b3527-147">Para evitar una fuga de memoria y permitir la recolección de elementos no utilizados en un componente que crea un objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, adopte uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="b3527-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="b3527-148">Elimine el objeto de la clase que ha creado la instancia de <xref:Microsoft.JSInterop.DotNetObjectReference>:</span><span class="sxs-lookup"><span data-stu-id="b3527-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="b3527-149">El patrón anterior que se muestra en la clase `ExampleJsInterop` también se puede implementar en un componente:</span><span class="sxs-lookup"><span data-stu-id="b3527-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="b3527-150">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b3527-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="b3527-151">Si el componente o la clase no elimina el objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, elimínelo en el cliente mediante la llamada a `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="b3527-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="b3527-152">Llamada a métodos de instancia de componente</span><span class="sxs-lookup"><span data-stu-id="b3527-152">Component instance method call</span></span>

<span data-ttu-id="b3527-153">Para invocar los métodos de .NET de un componente:</span><span class="sxs-lookup"><span data-stu-id="b3527-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="b3527-154">Use la función `invokeMethod` o `invokeMethodAsync` para hacer una llamada de método estático al componente.</span><span class="sxs-lookup"><span data-stu-id="b3527-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="b3527-155">El método estático del componente encapsula la llamada a su método de instancia como un objeto <xref:System.Action> invocado.</span><span class="sxs-lookup"><span data-stu-id="b3527-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="b3527-156">Para las aplicaciones Blazor Server, donde es posible que varios usuarios utilicen simultáneamente el mismo componente, use una clase auxiliar para invocar métodos de instancia.</span><span class="sxs-lookup"><span data-stu-id="b3527-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="b3527-157">Para obtener más información, vea la sección [Clase auxiliar de método de instancia de componente](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="b3527-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="b3527-158">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="b3527-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="b3527-159">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b3527-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="b3527-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="b3527-161">Para pasar argumentos al método de instancia:</span><span class="sxs-lookup"><span data-stu-id="b3527-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="b3527-162">Agregue parámetros a la invocación del método de JS.</span><span class="sxs-lookup"><span data-stu-id="b3527-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="b3527-163">En el ejemplo siguiente, se pasa un nombre al método.</span><span class="sxs-lookup"><span data-stu-id="b3527-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="b3527-164">Se pueden agregar parámetros adicionales a la lista según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b3527-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="b3527-165">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b3527-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="b3527-166">Proporcione los tipos correctos al objeto <xref:System.Action> para los parámetros.</span><span class="sxs-lookup"><span data-stu-id="b3527-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="b3527-167">Proporcione la lista de parámetros a los métodos de C#.</span><span class="sxs-lookup"><span data-stu-id="b3527-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="b3527-168">Invoque <xref:System.Action> (`UpdateMessage`) con los parámetros (`action.Invoke(name)`).</span><span class="sxs-lookup"><span data-stu-id="b3527-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="b3527-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="b3527-170">`message` de salida cuando se selecciona el botón **Call JS Method** (Llamar al método JS):</span><span class="sxs-lookup"><span data-stu-id="b3527-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="b3527-171">Clase auxiliar de método de instancia de componente</span><span class="sxs-lookup"><span data-stu-id="b3527-171">Component instance method helper class</span></span>

<span data-ttu-id="b3527-172">La clase auxiliar se usa para invocar un método de instancia como <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="b3527-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="b3527-173">Las clases auxiliares son útiles cuando:</span><span class="sxs-lookup"><span data-stu-id="b3527-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="b3527-174">Se representan varios componentes del mismo tipo en la misma página.</span><span class="sxs-lookup"><span data-stu-id="b3527-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="b3527-175">Se usa una aplicación Blazor Server, en la que es posible que varios usuarios utilicen un componente simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="b3527-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="b3527-176">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b3527-176">In the following example:</span></span>

* <span data-ttu-id="b3527-177">El componente `JSInteropExample` contiene varios componentes `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="b3527-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="b3527-178">Cada componente `ListItem` consta de un mensaje y un botón.</span><span class="sxs-lookup"><span data-stu-id="b3527-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="b3527-179">Cuando se selecciona un botón de componente `ListItem`, el método `UpdateMessage` de ese objeto `ListItem` cambia el texto del elemento de lista y oculta el botón.</span><span class="sxs-lookup"><span data-stu-id="b3527-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="b3527-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="b3527-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="b3527-181">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b3527-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="b3527-182">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="b3527-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="b3527-183">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-183">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JsRuntime

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
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="b3527-184">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="b3527-184">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="b3527-185">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="b3527-185">Avoid circular object references</span></span>

<span data-ttu-id="b3527-186">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="b3527-186">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="b3527-187">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="b3527-187">.NET method calls.</span></span>
* <span data-ttu-id="b3527-188">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="b3527-188">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="b3527-189">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="b3527-189">For more information, see the following issues:</span></span>

* <span data-ttu-id="b3527-190">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="b3527-190">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="b3527-191">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="b3527-191">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3527-192">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b3527-192">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="b3527-193">Ejemplo de `InteropComponent.razor` (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="b3527-193">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="b3527-194">Realización de transferencias de datos grandes en aplicaciones Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b3527-194">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
