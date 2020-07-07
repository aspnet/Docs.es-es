---
title: Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre cómo invocar métodos de .NET desde funciones de JavaScript en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 91f2aa893c06728b4b71d010241a2cb5a307ae0b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400198"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="48041-103">Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="48041-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="48041-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48041-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="48041-105">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="48041-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="48041-106">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="48041-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="48041-107">En este artículo se describe cómo invocar métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="48041-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="48041-108">Para obtener información sobre cómo llamar a funciones de JavaScript desde .NET, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="48041-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="48041-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48041-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="48041-110">Llamada al método estático de .NET</span><span class="sxs-lookup"><span data-stu-id="48041-110">Static .NET method call</span></span>

<span data-ttu-id="48041-111">Para invocar un método de .NET estático desde JavaScript, use las funciones `DotNet.invokeMethod` o `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="48041-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="48041-112">Pase el identificador del método estático al que quiere llamar, el nombre del ensamblado que contiene la función, y los argumentos.</span><span class="sxs-lookup"><span data-stu-id="48041-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="48041-113">La versión asincrónica es preferible para admitir escenarios de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="48041-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="48041-114">El método de .NET debe ser público y estático y debe tener el atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="48041-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="48041-115">Actualmente no se admite la llamada a métodos genéricos abiertos.</span><span class="sxs-lookup"><span data-stu-id="48041-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="48041-116">La aplicación de ejemplo incluye un método de C# para devolver una matriz `int`.</span><span class="sxs-lookup"><span data-stu-id="48041-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="48041-117">El atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) se aplica al método.</span><span class="sxs-lookup"><span data-stu-id="48041-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="48041-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="48041-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="48041-119">El archivo JavaScript servido al cliente invoca el método de .NET de C#.</span><span class="sxs-lookup"><span data-stu-id="48041-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="48041-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="48041-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="48041-121">Al seleccionar el botón **`Trigger .NET static method ReturnArrayAsync`** , examine la salida de la consola en las herramientas de desarrollo web del explorador.</span><span class="sxs-lookup"><span data-stu-id="48041-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="48041-122">La salida de la consola es:</span><span class="sxs-lookup"><span data-stu-id="48041-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="48041-123">El cuarto valor de matriz se inserta en la matriz (`data.push(4);`) devuelta por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="48041-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="48041-124">De forma predeterminada, el identificador del método es su nombre, pero puede especificar un identificador diferente mediante el constructor de atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):</span><span class="sxs-lookup"><span data-stu-id="48041-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="48041-125">En el archivo JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="48041-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="48041-126">Llamada a métodos de instancia</span><span class="sxs-lookup"><span data-stu-id="48041-126">Instance method call</span></span>

<span data-ttu-id="48041-127">También puede llamar a métodos de instancia de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="48041-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="48041-128">Para invocar un método de instancia de .NET desde JavaScript:</span><span class="sxs-lookup"><span data-stu-id="48041-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="48041-129">Pase la instancia de .NET por referencia a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="48041-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="48041-130">Realice una llamada estática a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="48041-130">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="48041-131">Encapsule la instancia de en una instancia <xref:Microsoft.JSInterop.DotNetObjectReference> y llame a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> en la instancia <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="48041-131">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="48041-132">Elimine los objetos <xref:Microsoft.JSInterop.DotNetObjectReference> (se incluye un ejemplo más adelante en esta sección).</span><span class="sxs-lookup"><span data-stu-id="48041-132">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="48041-133">Invoque métodos de instancia de .NET en la instancia mediante las funciones `invokeMethod` o `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="48041-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="48041-134">La instancia de .NET también se puede pasar como argumento al invocar otros métodos de .NET desde JavaScript.</span><span class="sxs-lookup"><span data-stu-id="48041-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="48041-135">La aplicación de ejemplo registra los mensajes en la consola del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="48041-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="48041-136">En los siguientes ejemplos de esta aplicación, examine la salida de la consola del explorador en las herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="48041-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="48041-137">Al seleccionar el botón **`Trigger .NET instance method HelloHelper.SayHello`** , se llama a `ExampleJsInterop.CallHelloHelperSayHello` y pasa un nombre, `Blazor`, al método.</span><span class="sxs-lookup"><span data-stu-id="48041-137">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="48041-138">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="48041-138">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="48041-139">`CallHelloHelperSayHello` invoca la función `sayHello` de JavaScript con una nueva instancia de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="48041-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="48041-140">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="48041-140">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="48041-141">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="48041-141">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="48041-142">El nombre se pasa al constructor de `HelloHelper`, que establece la propiedad `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="48041-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="48041-143">Cuando se ejecuta la función `sayHello` de JavaScript, `HelloHelper.SayHello` devuelve el mensaje `Hello, {Name}!`, y la función de JavaScript lo escribe en la consola.</span><span class="sxs-lookup"><span data-stu-id="48041-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="48041-144">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="48041-144">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="48041-145">Salida de la consola en las herramientas de desarrollo web del explorador:</span><span class="sxs-lookup"><span data-stu-id="48041-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="48041-146">Para evitar una fuga de memoria y permitir la recolección de elementos no utilizados en un componente que crea un objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, adopte uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="48041-146">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="48041-147">Elimine el objeto de la clase que ha creado la instancia de <xref:Microsoft.JSInterop.DotNetObjectReference>:</span><span class="sxs-lookup"><span data-stu-id="48041-147">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="48041-148">El patrón anterior que se muestra en la clase `ExampleJsInterop` también se puede implementar en un componente:</span><span class="sxs-lookup"><span data-stu-id="48041-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
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

* <span data-ttu-id="48041-149">Si el componente o la clase no elimina el objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, elimínelo en el cliente mediante la llamada a `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="48041-149">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="48041-150">Llamada a métodos de instancia de componente</span><span class="sxs-lookup"><span data-stu-id="48041-150">Component instance method call</span></span>

<span data-ttu-id="48041-151">Para invocar los métodos de .NET de un componente:</span><span class="sxs-lookup"><span data-stu-id="48041-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="48041-152">Use la función `invokeMethod` o `invokeMethodAsync` para hacer una llamada de método estático al componente.</span><span class="sxs-lookup"><span data-stu-id="48041-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="48041-153">El método estático del componente encapsula la llamada a su método de instancia como un objeto <xref:System.Action> invocado.</span><span class="sxs-lookup"><span data-stu-id="48041-153">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

<span data-ttu-id="48041-154">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="48041-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="48041-155">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="48041-155">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="48041-156">Cuando hay varios componentes, cada uno con métodos de instancia a los que llamar, se usa una clase auxiliar para invocar los métodos de instancia (como objetos <xref:System.Action>) de cada componente.</span><span class="sxs-lookup"><span data-stu-id="48041-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as <xref:System.Action>s) of each component.</span></span>

<span data-ttu-id="48041-157">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="48041-157">In the following example:</span></span>

* <span data-ttu-id="48041-158">El componente `JSInteropExample` contiene varios componentes `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="48041-158">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="48041-159">Cada componente `ListItem` consta de un mensaje y un botón.</span><span class="sxs-lookup"><span data-stu-id="48041-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="48041-160">Cuando se selecciona un botón de componente `ListItem`, el método `UpdateMessage` de ese objeto `ListItem` cambia el texto del elemento de lista y oculta el botón.</span><span class="sxs-lookup"><span data-stu-id="48041-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="48041-161">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="48041-161">`MessageUpdateInvokeHelper.cs`:</span></span>

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

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="48041-162">En el código JavaScript del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="48041-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="48041-163">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="48041-163">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="48041-164">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="48041-164">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="48041-165">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="48041-165">Avoid circular object references</span></span>

<span data-ttu-id="48041-166">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="48041-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="48041-167">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="48041-167">.NET method calls.</span></span>
* <span data-ttu-id="48041-168">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="48041-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="48041-169">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="48041-169">For more information, see the following issues:</span></span>

* <span data-ttu-id="48041-170">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="48041-170">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="48041-171">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="48041-171">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48041-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="48041-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="48041-173">Ejemplo de `InteropComponent.razor` (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="48041-173">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="48041-174">[Realización de transferencias de datos grandes en aplicaciones Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="48041-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
