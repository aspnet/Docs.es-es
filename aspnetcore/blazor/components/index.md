---
title: Creación y uso de componentes de Razor de ASP.NET Core
author: guardrex
description: Aprenda a crear y usar componentes de Razor, lo que incluye cómo enlazar a datos, controlar eventos y administrar los ciclos de vida de los componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: 0a8335461b4c9cd628d9c65b97f7ab6a74487fca
ms.sourcegitcommit: 7f423602a1475736f61fc361327d4de0976c9649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2020
ms.locfileid: "85950899"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Creación y uso de componentes de Razor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Tobias Bartsch](https://www.aveo-solutions.com/)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Las aplicaciones Blazor se crean usando *componentes*. Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario. Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario. Además, son flexibles y ligeros, y se pueden anidar, reutilizar y compartir entre proyectos.

## <a name="component-classes"></a>Clases de componentes

Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) (`.razor`) mediante una combinación de C# y marcado HTML. Un componente de Blazor se conoce formalmente como *componente de Razor* .

### <a name="razor-syntax"></a>Sintaxis de Razor

Los componentes Razor de las aplicaciones Blazor usan de manera generalizada la sintaxis de Razor. Si no está familiarizado con el lenguaje de marcado de Razor, se recomienda leer <xref:mvc/views/razor> antes de continuar.

Al acceder al contenido de la sintaxis Razor, preste especial atención a las siguientes secciones:

* [Directivas](xref:mvc/views/razor#directives): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analiza o funciona el marcado de los componentes.
* [Atributos de directiva](xref:mvc/views/razor#directive-attributes): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analizan o funcionan los elementos de los componentes.

### <a name="names"></a>Nombres

El nombre de un componente debe empezar por mayúsculas. Por ejemplo, `MyCoolComponent.razor` no es válido, pero `myCoolComponent.razor` sí.

### <a name="routing"></a>Enrutamiento

El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación. Cuando se compila un archivo de Razor con una directiva [`@page`][9], la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> que especifica la plantilla de ruta. En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada. Para obtener más información, vea <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>marcado

La interfaz de usuario de un componente se define mediante HTML. La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada *Razor* . Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente. El nombre de la clase generada coincide con el nombre del archivo.

Los miembros de la clase de componente se definen en un bloque [`@code`][1]. En el bloque [`@code`][1], el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente. Se permite emplear más de un bloque [`@code`][1].

Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`. Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo. En el siguiente ejemplo se evalúa y representa lo siguiente:

* `headingFontStyle` del valor de la propiedad CSS de `font-style`
* `headingText` del contenido del elemento `<h1>`

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos. Blazor compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).

Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto. Los componentes que generan páginas web suelen residir en la carpeta `Pages`. Los componentes que no son de página se colocan con frecuencia en la carpeta `Shared` o en una carpeta personalizada agregada al proyecto.

### <a name="namespaces"></a>Espacios de nombres

Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación. Así, si el espacio de nombres raíz de la aplicación es `BlazorApp` y el componente `Counter` reside en la carpeta `Pages`:

* El espacio de nombres del componente `Counter` es `BlazorApp.Pages`.
* El nombre de tipo completo del componente es `BlazorApp.Pages.Counter`.

En el caso de las carpetas personalizadas que contienen componentes, agregue una directiva [`@using`][2] al componente principal o al archivo `_Imports.razor` de la aplicación. En el ejemplo siguiente se ponen a disposición los componentes de la carpeta `Components`:

```razor
@using BlazorApp.Components
```

También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`][2]:

```razor
<BlazorSample.Components.MyComponent />
```

El espacio de nombres de un componente creado con Razor se basa en (por orden de prioridad):

* Designación [`@namespace`][8] del marcado del archivo de Razor (`.razor`) (`@namespace BlazorSample.MyNamespace`).
* Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).
* Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto (`.csproj`) y la ruta de acceso de la raíz del proyecto al componente. Por ejemplo, el marco de trabajo resuelve `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) en el espacio de nombres `BlazorSample.Pages`. Los componentes de C# siguen las reglas de los enlaces de nombres. En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:
  * Ejecute `Pages` en la misma carpeta.
  * Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.

> [!NOTE]
> La calificación `global::` no se puede usar.
>
> No se pueden importar componentes con instrucciones [`using`](/dotnet/csharp/language-reference/keywords/using-statement) con alias (por ejemplo, `@using Foo = Bar`).
>
> No se pueden usar nombres parciales. Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia al componente `NavMenu` (`NavMenu.razor`) con `<Shared.NavMenu></Shared.NavMenu>`.

### <a name="partial-class-support"></a>Compatibilidad parcial de clases

Los componentes de Razor se generan como clases parciales. Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:

* Se define código de C# en un bloque [`@code`][1] con marcado HTML y código de Razor en un solo archivo. Las plantillas de Blazor definen sus componentes de Razor con este método.
* El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.

En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque [`@code`][1] en una aplicación generada a partir de una plantilla de Blazor. El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Agregue los espacios de nombres que sean necesarios al archivo de clase parcial. Los espacios de nombres típicos usados por los componentes de Razor incluyen:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

### <a name="specify-a-base-class"></a>Especificación de una clase base

La directiva [`@inherits`][6] se puede usar para especificar la clase base de un componente. En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente. La clase base debe derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase>.

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>Uso de componentes

Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML. El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.

El siguiente marcado en `Pages/Index.razor` representa una instancia de `HeadingComponent`:

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado. Al agregar una directiva [`@using`][2] relativa al espacio de nombres del componente, se permite que el componente esté disponible, lo que resuelve la advertencia.

## <a name="parameters"></a>Parámetros

### <a name="route-parameters"></a>Parámetros de ruta

Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva [`@page`][9]. El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

No se admiten parámetros opcionales, por lo que en el ejemplo anterior se aplican dos directivas [`@page`][9]. La primera permite navegar al componente sin un parámetro, mientras que la segunda directiva [`@page`][9] recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.

Los componentes de Razor (`.razor`) **no** admiten la sintaxis de parámetro *comodín* (`*`/`**`), que captura la ruta de acceso a lo largo de varios límites de carpeta.

### <a name="component-parameters"></a>Parámetros del componente

Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas en la clase del componente con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute). Use atributos para especificar argumentos para un componente en el marcado.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> No cree componentes que escriban en sus propios *parámetros de componente*; en su lugar, use un campo privado. Para más información, consulte la sección [No crear componentes que escriban en sus propias propiedades de parámetro](#dont-create-components-that-write-to-their-own-parameter-properties).

## <a name="child-content"></a>Contenido secundario

Los componentes pueden definir el contenido de otro componente. El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.

En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment>, que representa a su vez un segmento de interfaz de usuario que se va a representar. El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse. El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La propiedad que recibe el contenido de <xref:Microsoft.AspNetCore.Components.RenderFragment> debe denominarse `ChildContent` por convención.

El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Expansión de atributos y parámetros arbitrarios

Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente. Los atributos adicionales se pueden capturar en un diccionario y luego *expandirse* en un elemento cuando el componente se representa por medio de la directiva de Razor [`@attributes`][3]. Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones. Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.

En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` con claves de cadena. El uso de `IReadOnlyDictionary<string, object>` también es una opción en este escenario.

Los elementos `<input>` representados con ambos métodos son idénticos:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Para aceptar atributos arbitrarios, defina un parámetro de componente usando el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) con la propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> establecida en `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro. Un componente solamente puede definir un parámetro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>. El tipo de propiedad que se usa con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> se debe poder asignar desde `Dictionary<string, object>` con claves de cadena. `IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.

La posición de [`@attributes`][3] relativa a la posición de los atributos de elemento es importante. Cuando [`@attributes`][3] se expande en el elemento, los atributos se procesan de derecha a izquierda (del último al primero). Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

El atributo `extra` del componente `Child` se establece a la derecha de [`@attributes`][3]. El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):

```html
<div extra="5" />
```

En el ejemplo siguiente, el orden de `extra` y [`@attributes`][3] se invierte en el elemento `Child` del componente `<div>`:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Captura de referencias a componentes

Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`. Para capturar una referencia de componente:

* Agregue un atributo [`@ref`][4] al componente secundario.
* Defina un campo con el mismo tipo que el componente secundario.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Cuando el componente se represente, el campo `loginDialog` se rellena con la instancia del componente secundario `MyLoginDialog`. Tras ello, se pueden invocar métodos de .NET en la instancia del componente.

> [!IMPORTANT]
> La variable `loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`. Hasta que se represente el componente, no hay nada a lo que hacer referencia.
>
> Para manipular las referencias de componente una vez finalizada la representación del componente, use los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).
>
> Para usar una variable de referencia con un controlador de eventos, use una expresión lambda o asigne el delegado de controlador de eventos en los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render). Esto garantiza que la variable de referencia se asigna antes de que se asigne el controlador de eventos.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Para hacer referencias a componentes de un bucle, consulte el artículo sobre la [captura de referencias a varios componentes secundarios similares (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript. Las referencias de componente no se pasan al código JavaScript. Solo se usan en código .NET.

> [!NOTE]
> **No** use referencias de componentes para mutar el estado de los componentes secundarios. En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios. El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.

## <a name="synchronization-context"></a>Contexto de sincronización

Blazor usa un contexto de sincronización (<xref:System.Threading.SynchronizationContext>) para aplicar un único subproceso lógico de ejecución. Los [métodos de ciclo de vida](xref:blazor/components/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en el contexto de sincronización.

El contexto de sincronización de Blazor Server intenta emular un entorno de un solo subproceso para que coincida estrechamente con el modelo WebAssembly en el explorador, que es de un solo subproceso. En todo momento, el trabajo se realiza en exactamente un subproceso, lo que da la impresión de un único subproceso lógico. Nunca se ejecutan dos operaciones simultáneamente.

### <a name="avoid-thread-blocking-calls"></a>Evitar las llamadas de bloqueo de subprocesos

Por lo general, no llame a los métodos siguientes. Los métodos siguientes bloquean el subproceso y, por tanto, impiden que la aplicación reanude el trabajo hasta que se complete <xref:System.Threading.Tasks.Task>:

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Invocación de métodos de componentes externamente para actualizar el estado

En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que envía al contexto de sincronización de Blazor. Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registre el elemento `NotifierService` como un singleton:

* En Blazor WebAssembly, registre el servicio en `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* En Blazor Server, registre el servicio en `Startup.ConfigureServices`:

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Use el elemento `NotifierService` para actualizar un componente:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

En el ejemplo anterior, `NotifierService` invoca el método `OnNotify` del componente fuera del contexto de sincronización de Blazor. `InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Uso de \@key para controlar la conservación de elementos y componentes

Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos. Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.

Considere el ejemplo siguiente:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas. Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`. Esto puede hacer que se genere una nueva representación más compleja de lo esperado. En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.

El proceso de asignación se puede controlar con el atributo de directiva [`@key`][5]. [`@key`][5] hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:

* Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente. Las demás instancias permanecerán inalteradas.
* Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente. Las demás instancias permanecerán inalteradas.
* Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.

En algunos escenarios, el uso de [`@key`][5] reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.

> [!IMPORTANT]
> Las claves son locales de cada componente o elemento contenedor. Las claves no se comparan globalmente en todo el documento.

### <a name="when-to-use-key"></a>Cuándo usar \@key

Normalmente, usar [`@key`][5] tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) y haya un valor adecuado para definir el elemento [`@key`][5].

También se puede usar [`@key`][5] para impedir que Blazor conserve un subárbol de componentes o elementos cuando un objeto cambie:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Si `@currentPerson` cambia, la directiva de atributo [`@key`][5] fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes. Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.

### <a name="when-not-to-use-key"></a>Cuándo no usar \@key

Las comparaciones con [`@key`][5] repercuten en el rendimiento. El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar [`@key`][5] únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.

Aun cuando [`@key`][5] no se use, Blazor conserva las instancias de componentes y elementos secundarios lo máximo posible. La única ventaja de utilizar [`@key`][5] es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.

### <a name="what-values-to-use-for-key"></a>Qué valores usar en \@key

Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en [`@key`][5]:

* Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior). Esto garantiza la conservación en función de la igualdad de las referencias de objetos.
* Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).

Asegúrese de que los valores usados en [`@key`][5] no entran en conflicto. Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes. Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>No crear componentes que escriban en sus propias propiedades de parámetro

Los parámetros se sobrescriben en las condiciones siguientes:

* El contenido de un componente secundario se representa con un <xref:Microsoft.AspNetCore.Components.RenderFragment>.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se llama en el componente principal.

Los parámetros se restablecen porque el componente principal se vuelve a representar cuando se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> y se suministran valores de parámetro nuevos al componente secundario.

Considere el componente `Expander` siguiente que:

* Representa el contenido secundario.
* Alterna la visualización del contenido secundario con un parámetro de componente.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

El componente `Expander` se agrega a un componente principal que podría llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Al principio, los componentes `Expander` se comportan de manera independiente cuando se alternan sus propiedades de `Expanded`. Los componentes secundarios mantienen sus estados según lo previsto. Cuando se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en el componente principal, el parámetro `Expanded` del primer componente secundario se restablece nuevamente en su valor inicial (`true`). No se restablece el valor `Expanded` del segundo componente de `Expander`, porque no se representa ningún contenido secundario en el segundo componente.

Para mantener el estado en el escenario anterior, use un *campo privado* en el componente `Expander` para mantener su estado de alternancia.

El componente `Expander` siguiente:

* Acepta el valor del parámetro de componente `Expanded` del componente principal.
* Asigna el valor del parámetro de componente a un *campo privado* (`expanded`) en el [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).
* Usa el campo privado para mantener su estado de alternancia interno.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a>Aplicación de un atributo

En los componentes de Razor se pueden aplicar atributos con la directiva [`@attribute`][7]. En el siguiente ejemplo se aplica el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a la clase del componente:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Atributos de elementos HTML condicionales

Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET. Si el valor es `false` o `null`, el atributo no se representa. Si el valor es `true`, el atributo se representa minimizado.

En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Si `IsCompleted` es `true`, la casilla se representa así:

```html
<input type="checkbox" checked />
```

Si `IsCompleted` es `false`, la casilla se representa así:

```html
<input type="checkbox" />
```

Para obtener más información, vea <xref:mvc/views/razor>.

> [!WARNING]
> Algunos atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`. En esos casos, use un tipo `string` en lugar de `bool`.

## <a name="raw-html"></a>HTML sin formato

Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal. Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`. El valor se analiza como HTML o SVG y se inserta en el DOM.

> [!WARNING]
> La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.

En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Plantillas de Razor

Los fragmentos de representación se pueden definir mediante la sintaxis de plantilla de Razor. Las plantillas de Razor son una forma de definir un fragmento de interfaz de usuario y asumen el siguiente formato:

```razor
@<{HTML tag}>...</{HTML tag}>
```

En el siguiente ejemplo se muestra cómo especificar los valores <xref:Microsoft.AspNetCore.Components.RenderFragment> y <xref:Microsoft.AspNetCore.Components.RenderFragment%601> y las plantillas de representación directamente en un componente. Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/components/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Salida representada del código anterior:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>Recursos estáticos

Blazor sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [`web root (wwwroot)` del proyecto](xref:fundamentals/index#web-root).

Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático. En el ejemplo siguiente, `logo.png` se encuentra físicamente en la carpeta `{PROJECT ROOT}/wwwroot/images`:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Los componentes de Razor **no** admiten la notación de virgulilla-barra diagonal (`~/`).

Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Las aplicaciones auxiliares de etiquetas no se admiten en los componentes

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) no se admiten en los componentes de Razor (archivos `.razor`). Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.

## <a name="scalable-vector-graphics-svg-images"></a>Imágenes con formato Scalable Vector Graphics (SVG)

Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) (`.svg`) mediante la etiqueta `<img>`:

```html
<img alt="Example image" src="some-image.svg" />
```

Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos (`.css`):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Pero no todos los escenarios admiten el uso de marcado SVG en línea. Si se coloca una etiqueta `<svg>` directamente en un archivo de componente (`.razor`), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos. Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y [`@bind`][10] no se puede usar con algunas etiquetas SVG. Para más información, consulte la [compatibilidad con SVG en Blazor(dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/server/threat-mitigation>: incluye instrucciones sobre cómo crear aplicaciones de Blazor Server que deben afrontar situaciones de agotamiento de recursos.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
