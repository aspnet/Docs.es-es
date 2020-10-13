---
title: Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo invocar funciones de JavaScript con métodos de .NET en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: d36140067ba6e75f2d00cb86ea488e40d28bd86f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762170"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa. Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).

En este artículo se describe cómo invocar funciones de JavaScript desde .NET. Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Para llamar a JavaScript desde .NET, use la abstracción <xref:Microsoft.JSInterop.IJSRuntime>. Para emitir llamadas de interoperabilidad de JS, inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> toma un identificador de la función de JavaScript que se quiere invocar junto con un número cualquiera de argumentos serializables con JSON. El identificador de función es relativo al ámbito global (`window`). Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`. No es necesario registrar la función para poder llamarla. El tipo de valor devuelto `T` también debe ser serializable con JSON. `T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.

Se llama con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> a las funciones de JavaScript que devuelven una [promesa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). `InvokeAsync` desencapsula la promesa y devuelve el valor que esta espera.

En el caso de las aplicaciones Blazor Server que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial. Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador. Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).

El siguiente ejemplo se basa en [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript. En el ejemplo se muestra cómo invocar una función de JavaScript desde un método de C# que descarga un requisito del código de desarrollador a una API de JavaScript existente. La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.

Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript (`.js`) con una referencia al archivo de script:

```html
<script src="exampleJsInterop.js"></script>
```

El siguiente componente:

* Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente ( **`Convert Array`** ).
* Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena, que se devuelve al componente para que la muestre.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Para usar la abstracción <xref:Microsoft.JSInterop.IJSRuntime>, adopte cualquiera de los siguientes métodos:

* Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en el componente de Razor (`.razor`):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`. Se llama a la función con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> y no se devuelve un valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Inserte la abstracción <xref:Microsoft.JSInterop.IJSRuntime> en una clase (`.cs`):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Dentro del elemento `<head>` de `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server), proporcione una función de JavaScript `handleTickerChanged`. Se llama a la función con `JSRuntime.InvokeAsync` y se devuelve un valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:

* `showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.
* `displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo `wwwroot/index.html` (Blazor WebAssembly) o en el archivo `Pages/_Host.cshtml` (Blazor Server).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.

Los métodos de .NET interoperan con las funciones de JavaScript en el archivo `exampleJsInterop.js` llamando a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.

La abstracción <xref:Microsoft.JSInterop.IJSRuntime> es asincrónica para dar cabida a escenarios de servidor Blazor Server. Si la aplicación es una aplicación Blazor WebAssembly y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado <xref:Microsoft.JSInterop.IJSInProcessRuntime> y llame a <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> en su lugar. Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Para permitir el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar, vea la sección [Aislamiento de JavaScript y referencias a objetos en Blazor](#blazor-javascript-isolation-and-object-references).

::: moniker-end

La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS. El componente:

* Recibe la entrada del usuario a través de un mensaje de JavaScript.
* Devuelve el texto al componente para que lo procese.
* Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.

`Pages/JsInterop.razor`:

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

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

1. Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **`Trigger JavaScript Prompt`** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo `wwwroot/exampleJsInterop.js`.
1. La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente. El componente almacena el nombre del usuario en una variable local, `name`.
1. La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.

## <a name="call-a-void-javascript-function"></a>Llamada a una función de JavaScript vacía

Las funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) se llaman con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Detección de cuándo se está obteniendo una representación previa de la aplicación Blazor Server
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Captura de referencias a elementos

Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML. Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.

Use el siguiente método para capturar referencias a elementos HTML en un componente:

* Agregue un atributo `@ref` al elemento HTML.
* Defina un campo de tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cuyo nombre coincida con el valor del atributo `@ref`.

En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor. Este escenario es útil cuando una API de terceros proporciona contenido al elemento. Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.
>
> En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):
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
> Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.

En lo que respecta al código .NET, un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> es un identificador opaco. *Lo único* que se puede hacer con <xref:Microsoft.AspNetCore.Components.ElementReference> es pasarlo a código de JavaScript a través de la interoperabilidad de JS. Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.

Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Para llamar a una función de JavaScript que no devuelve un valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>. El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento <xref:Microsoft.AspNetCore.Components.ElementReference> capturado:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de <xref:Microsoft.JSInterop.IJSRuntime>:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Se llama al método `Focus` directamente en el objeto. En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La variable `username` solo se rellena después de que el componente se represente. Si se pasa un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`. Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los métodos de ciclo de vida del componente [`OnAfterRenderAsync` u `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).

Cuando se trabaje con tipos genéricos y se devuelva un valor, use <xref:System.Threading.Tasks.ValueTask%601>:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

Se llama al método `GenericMethod` directamente en el objeto con un tipo. En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Referencia a elementos en componentes

La validez de un elemento <xref:Microsoft.AspNetCore.Components.ElementReference> solo está garantizada en el método de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> de un componente (y una referencia de elemento es un `struct`), por lo que una referencia de elemento no se puede pasar entre componentes.

Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:

* Permitir que los componentes secundarios registren devoluciones de llamada.
* Invoca las devoluciones de llamada registradas durante el evento <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> con la referencia de elemento que se ha pasado. Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.

En el ejemplo siguiente Blazor WebAssembly se muestra este enfoque.

En el elemento `<head>` de `wwwroot/index.html`:

```html
<style>
    .red { color: red }
</style>
```

En el elemento `<body>` de `wwwroot/index.html`:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (componente primario):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

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

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

`Shared/SurveyPrompt.razor` (componente secundario):

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

`Shared/SurveyPrompt.razor.cs`:

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

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample`).

## <a name="harden-js-interop-calls"></a>Protección de las llamadas de interoperabilidad de JS

La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable. De forma predeterminada, una aplicación Blazor Server agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto. Si una aplicación puede tolerar un tiempo de espera más restrictivo, establézcalo recurriendo a uno de los siguientes métodos:

* Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Para más información sobre el agotamiento de recursos, vea <xref:blazor/security/server/threat-mitigation>.

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitar referencias de objetos circulares

Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:

* Llamadas de método .NET.
* Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.

Para más información, consulte los problemas siguientes:

* [Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525
* [Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Aislamiento de JavaScript y referencias a objetos en Blazor

Blazor permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar. El aislamiento de JavaScript reporta las siguientes ventajas:

* El código JavaScript importado no contamina el espacio de nombres global.
* No es necesario que los consumidores de una biblioteca y componentes importen el código JavaScript relacionado.

Por ejemplo, el siguiente módulo de JavaScript exporta una función de JavaScript para mostrar un mensaje del explorador:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Agregue el módulo de JavaScript anterior a una biblioteca de .NET como un recurso web estático (`wwwroot/exampleJsInterop.js`) y, después, importe dicho módulo al código .NET usando el servicio <xref:Microsoft.JSInterop.IJSRuntime>. El servicio se inserta como `jsRuntime` (no se muestra) en el siguiente ejemplo:

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

El identificador de `import` del ejemplo anterior es un identificador especial que se usa específicamente para importar un módulo de JavaScript. Especifique el módulo por medio de su ruta de acceso de recurso web estático estable: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`. El marcador de posición `{LIBRARY NAME}` es el nombre de la biblioteca. El marcador de posición `{PATH UNDER WWWROOT}` es la ruta de acceso al script en `wwwroot`.

<xref:Microsoft.JSInterop.IJSRuntime> importa el módulo como un elemento `IJSObjectReference`, que es una referencia a un objeto de JavaScript hecha desde código .NET. Use `IJSObjectReference` para invocar funciones de JavaScript exportadas desde el módulo:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar de forma sincrónica.

`IJSUnmarshalledObjectReference` representa una referencia a un objeto de JavaScript cuyas funciones se pueden invocar sin la sobrecarga de serializar los datos de .NET. Se puede usar en Blazor WebAssembly cuando el rendimiento es crucial:

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
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

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Uso de las bibliotecas de JavaScript que representan la interfaz de usuario (elementos DOM)

En ocasiones, es posible que quiera usar bibliotecas de JavaScript que generen elementos de la interfaz de usuario visibles en el DOM del explorador. A primera vista, esto podría parecer difícil porque el sistema de comparación de Blazor se basa en tener control sobre el árbol de elementos DOM y encuentra errores si algún código externo muta el árbol DOM e invalida su mecanismo para aplicar las comparaciones. No se trata de una limitación específica de Blazor. El mismo desafío se produce con cualquier marco de interfaz de usuario basado en comparaciones.

Afortunadamente, es sencillo insertar una interfaz de usuario generada externamente dentro de una interfaz de usuario de componentes de Blazor de forma confiable. La técnica recomendada consiste en hacer que el código del componente (archivo `.razor`) produzca un elemento vacío. En lo que se refiere al sistema de comparación de Blazor, el elemento siempre está vacío, por lo que el representador no recorre en el elemento y no interferirá con sus contenidos. Esto hace que sea seguro rellenar el elemento con contenido arbitrario administrado externamente.

En el siguiente ejemplo se muestra el concepto. En la instrucción `if` en la que `firstRender` es `true`, haga algo con `myElement`. Por ejemplo, llame a una biblioteca de JavaScript externa para rellenarla. Blazor no hará nada con el contenido del elemento hasta que se quite este componente. Cuando se quite el componente, también se quitará el subárbol DOM completo del componente.

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

Como ejemplo más detallado, considere el siguiente componente que representa un mapa interactivo mediante las [API de código abierto de Mapbox](https://www.mapbox.com/):

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

El módulo de JavaScript correspondiente, que debe colocarse en `wwwroot/mapComponent.js`, es el siguiente:

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

En el ejemplo anterior, reemplace la cadena `{ACCESS TOKEN}` con un token de acceso válido que puede obtener de https://account.mapbox.com.

Para generar el estilo correcto, agregue la siguiente etiqueta de hoja de estilos a la página HTML del host (`index.html` o `_Host.cshtml`):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

En el ejemplo anterior se genera una interfaz de usuario de mapa interactiva, en la que el usuario:

* Puede arrastrar para desplazarse o hacer zoom.
* Hacer clic en los botones para saltar a ubicaciones predefinidas.

![Mapa callejero de Mapbox de Tokio, Japón, con botones para seleccionar Bristol, Reino Unido, y Tokio, Japón](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Los puntos clave que se deben comprender son:

 * En cuanto a Blazor, `<div>` con `@ref="mapElement"` se deja vacío. Por lo tanto, es seguro que `mapbox-gl.js` la rellene y modifique su contenido con el tiempo. Puede usar esta técnica con cualquier biblioteca de JavaScript que represente la interfaz de usuario. Incluso podría insertar componentes de un marco de JavaScript SPA de terceros dentro de los componentes de Blazor, siempre y cuando no intenten modificar otras partes de la página. *No* es seguro que el código JavaScript externo modifique los elementos que Blazor no considere vacíos.
 * Al utilizar este enfoque, tenga en cuenta las reglas sobre cómo Blazor conserva o destruye los elementos DOM. En el ejemplo anterior, el componente controla de forma segura los eventos de clic de botón y actualiza la instancia del mapa existente porque, de forma predeterminada, los elementos DOM se conservan siempre que sea posible. Si estuviera representando una lista de elementos de mapa desde dentro de un bucle `@foreach`, querría usar `@key` para garantizar la preservación de las instancias del componente. De lo contrario, los cambios en los datos de la lista podrían hacer que las instancias del componente conservaran el estado de las instancias anteriores de forma no deseada. Para obtener más información, vea el artículo sobre el [uso de @key para conservar elementos y componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Además, en el ejemplo anterior se muestra cómo es posible encapsular la lógica de JavaScript y las dependencias dentro de un módulo ES6, y cargarlo dinámicamente mediante el identificador `import`. Para más información, consulte [Aislamiento de JavaScript y referencias a objetos](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/call-dotnet-from-javascript>
* [Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Realización de transferencias de datos grandes en aplicaciones Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
