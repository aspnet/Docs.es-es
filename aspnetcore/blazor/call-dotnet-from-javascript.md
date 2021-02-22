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
ms.openlocfilehash: 45ddcc9e006df2c5e86a7859efc76882b269a496
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280395"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor

Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa. Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).

En este artículo se describe cómo invocar métodos de .NET desde JavaScript. Para obtener información sobre cómo llamar a funciones de JavaScript desde .NET, vea <xref:blazor/call-javascript-from-dotnet>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

> [!NOTE]
> Agregue archivos JS (etiquetas de `<script>`) antes de la etiqueta `</body>` de cierre en el archivo `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server). Asegúrese de que los archivos JS con métodos de interoperabilidad de JS se incluyen antes que los archivos JS del marco Blazor.

## <a name="static-net-method-call"></a>Llamada al método estático de .NET

Para invocar un método de .NET estático desde JavaScript, use las funciones `DotNet.invokeMethod` o `DotNet.invokeMethodAsync`. Pase el identificador del método estático al que quiere llamar, el nombre del ensamblado que contiene la función, y los argumentos. La versión asincrónica es preferible para admitir escenarios de Blazor Server. El método de .NET debe ser público y estático, y debe tener el [atributo `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute). Actualmente no se admite la llamada a métodos genéricos abiertos.

La aplicación de ejemplo incluye un método de C# para devolver una matriz `int`. El [atributo `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) se aplica al método.

`Pages/JsInterop.razor`:

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

El archivo JavaScript servido al cliente invoca el método de .NET de C#.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Al seleccionar el botón **`Trigger .NET static method ReturnArrayAsync`** , examine la salida de la consola en las herramientas de desarrollo web del explorador.

La salida de la consola es:

```console
Array(4) [ 1, 2, 3, 4 ]
```

El cuarto valor de matriz se inserta en la matriz (`data.push(4);`) devuelta por `ReturnArrayAsync`.

De forma predeterminada, el identificador del método es su nombre, pero puede especificar un identificador diferente mediante el constructor del [atributo `[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

En el archivo JavaScript del lado cliente:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

## <a name="instance-method-call"></a>Llamada a métodos de instancia

También puede llamar a métodos de instancia de .NET desde JavaScript. Para invocar un método de instancia de .NET desde JavaScript:

* Pase la instancia de .NET por referencia a JavaScript:
  * Realice una llamada estática a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.
  * Encapsule la instancia de en una instancia <xref:Microsoft.JSInterop.DotNetObjectReference> y llame a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> en la instancia <xref:Microsoft.JSInterop.DotNetObjectReference>. Elimine los objetos <xref:Microsoft.JSInterop.DotNetObjectReference> (se incluye un ejemplo más adelante en esta sección).
* Invoque métodos de instancia de .NET en la instancia mediante las funciones `invokeMethod` o `invokeMethodAsync`. La instancia de .NET también se puede pasar como argumento al invocar otros métodos de .NET desde JavaScript.

> [!NOTE]
> La aplicación de ejemplo registra los mensajes en la consola del lado cliente. En los siguientes ejemplos de esta aplicación, examine la salida de la consola del explorador en las herramientas de desarrollo del explorador.

Al seleccionar el botón **`Trigger .NET instance method HelloHelper.SayHello`** , se llama a `ExampleJsInterop.CallHelloHelperSayHello` y pasa un nombre, `Blazor`, al método.

`Pages/JsInterop.razor`:

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

`CallHelloHelperSayHello` invoca la función `sayHello` de JavaScript con una nueva instancia de `HelloHelper`.

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

El nombre se pasa al constructor de `HelloHelper`, que establece la propiedad `HelloHelper.Name`. Cuando se ejecuta la función `sayHello` de JavaScript, `HelloHelper.SayHello` devuelve el mensaje `Hello, {Name}!`, y la función de JavaScript lo escribe en la consola.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Salida de la consola en las herramientas de desarrollo web del explorador:

```console
Hello, Blazor!
```

Para evitar una fuga de memoria y permitir la recolección de elementos no utilizados en un componente que crea un objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, adopte uno de los enfoques siguientes:

* Elimine el objeto de la clase que ha creado la instancia de <xref:Microsoft.JSInterop.DotNetObjectReference>:

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

  El patrón anterior que se muestra en la clase `ExampleJsInterop` también se puede implementar en un componente:

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
  
  El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

* Si el componente o la clase no elimina el objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, elimínelo en el cliente mediante la llamada a `.dispose()`:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Llamada a métodos de instancia de componente

Para invocar los métodos de .NET de un componente:

* Use la función `invokeMethod` o `invokeMethodAsync` para hacer una llamada de método estático al componente.
* El método estático del componente encapsula la llamada a su método de instancia como un objeto <xref:System.Action> invocado.

> [!NOTE]
> Para las aplicaciones Blazor Server, donde es posible que varios usuarios utilicen simultáneamente el mismo componente, use una clase auxiliar para invocar métodos de instancia.
>
> Para obtener más información, vea la sección [Clase auxiliar de método de instancia de componente](#component-instance-method-helper-class).

En el código JavaScript del lado cliente:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

`Pages/JSInteropComponent.razor`:

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

Para pasar argumentos al método de instancia:

* Agregue parámetros a la invocación del método de JS. En el ejemplo siguiente, se pasa un nombre al método. Se pueden agregar parámetros adicionales a la lista según sea necesario.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

* Proporcione los tipos correctos al objeto <xref:System.Action> para los parámetros. Proporcione la lista de parámetros a los métodos de C#. Invoque <xref:System.Action> (`UpdateMessage`) con los parámetros (`action.Invoke(name)`).

  `Pages/JSInteropComponent.razor`:

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

  `message` de salida cuando se selecciona el botón **Call JS Method** (Llamar al método JS):

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Clase auxiliar de método de instancia de componente

La clase auxiliar se usa para invocar un método de instancia como <xref:System.Action>. Las clases auxiliares son útiles cuando:

* Se representan varios componentes del mismo tipo en la misma página.
* Se usa una aplicación Blazor Server, en la que es posible que varios usuarios utilicen un componente simultáneamente.

En el ejemplo siguiente:

* El componente `JSInteropExample` contiene varios componentes `ListItem`.
* Cada componente `ListItem` consta de un mensaje y un botón.
* Cuando se selecciona un botón de componente `ListItem`, el método `UpdateMessage` de ese objeto `ListItem` cambia el texto del elemento de lista y oculta el botón.

`MessageUpdateInvokeHelper.cs`:

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

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

En el código JavaScript del lado cliente:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

`Shared/ListItem.razor`:

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

`Pages/JSInteropExample.razor`:

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

## <a name="avoid-circular-object-references"></a>Evitar referencias de objetos circulares

Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:

* Llamadas de método .NET.
* Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.

Para más información, consulte los problemas siguientes:

* [Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525
* [Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)

## <a name="size-limits-on-js-interop-calls"></a>Límites de tamaño en las llamadas de interoperabilidad de JS

En Blazor WebAssembly, el marco no impone límites en cuanto al tamaño de las entradas y salidas de las llamadas de interoperabilidad de JS.

En Blazor Server, las llamadas de interoperabilidad de JS presentan un tamaño limitado por el tamaño máximo de los mensajes SignalR entrantes que se permite para los métodos del concentrador. Esto se aplica por medio de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (valor predeterminado: 32 KB). Los mensajes SignalR de JS a .NET de más de <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> generan un error. El marco no impone ningún límite de tamaño para un mensaje SignalR desde el concentrador a un cliente.

Cuando el registro de SignalR no está establecido en [Depurar](xref:Microsoft.Extensions.Logging.LogLevel) o [Seguimiento](xref:Microsoft.Extensions.Logging.LogLevel), solo aparece un error relativo al tamaño del mensaje en la consola de herramientas de desarrollo del explorador:

> Error: Conexión desconectada con el error "Error: El servidor ha devuelto un error al cerrarse: Conexión cerrada con un error.".

Cuando el [registro del lado servidor de SignalR](xref:signalr/diagnostics#server-side-logging) se establece en [Depurar](xref:Microsoft.Extensions.Logging.LogLevel) o [Seguimiento](xref:Microsoft.Extensions.Logging.LogLevel), el registro del lado servidor inicia una excepción <xref:System.IO.InvalidDataException> relativa a un error del tamaño del mensaje.

`appsettings.Development.json`:

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

> System.IO.InvalidDataException: Se ha superado el tamaño máximo del mensaje de 32768B. El tamaño del mensaje se puede configurar en AddHubOptions.

Para aumentar el límite, establezca <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> en `Startup.ConfigureServices`. En el ejemplo siguiente se establece el tamaño máximo del mensaje de recepción en 64 KB (64*1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

El aumento del límite de tamaño del mensaje entrante SignalR implica que se necesitan más recursos del servidor y lo expone a más riesgos por parte de un usuario malintencionado. Además, la lectura de una gran cantidad de contenido en la memoria, como cadenas o matrices de bytes, también puede dar lugar a que las asignaciones funcionen de forma deficiente con el recolector de elementos no utilizados, lo que puede reducir significativamente el rendimiento.

Una opción para leer cargas grandes consiste en enviar el contenido en fragmentos más pequeños y procesar la carga como <xref:System.IO.Stream>. Se puede usar al leer cargas grandes de JSON o si los datos están disponibles en JavaScript como bytes sin formato. Para obtener un ejemplo en el que se muestra el envío de cargas binarias de gran tamaño en Blazor Server que usa técnicas similares a las del componente `InputFile`, consulte la [aplicación de ejemplo para envíos binarios](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Tenga en cuenta la guía siguiente al desarrollar código que transfiera un gran volumen de datos entre JavaScript y Blazor:

* Segmente los datos en partes más pequeñas y envíe los segmentos de datos secuencialmente hasta que el servidor reciba todos los datos.
* No asigne objetos grandes en código JavaScript y C#.
* No bloquee el subproceso de interfaz de usuario principal durante períodos largos al enviar o recibir datos.
* Libere la memoria consumida al completar o cancelar el proceso.
* Aplique los requisitos adicionales siguientes por motivos de seguridad:
  * Declare el tamaño máximo del archivo o los datos que se pueden pasar.
  * Declare la tasa mínima de carga desde el cliente al servidor.
* Después de que el servidor reciba los datos, los datos se pueden:
  * Almacenar temporalmente en un búfer de memoria hasta que se recopilen todos los segmentos.
  * Consumir inmediatamente. Por ejemplo, los datos se pueden almacenar inmediatamente en una base de datos o escribir en el disco a medida que se reciba cada segmento.

## <a name="js-modules"></a>Módulos de JS

En el caso del aislamiento de JS, la interoperabilidad de JS funciona con la compatibilidad predeterminada del explorador para los [módulos EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificación de ECMAScript](https://tc39.es/ecma262/#sec-modules)).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/call-javascript-from-dotnet>
* [Ejemplo de `InteropComponent.razor` (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
