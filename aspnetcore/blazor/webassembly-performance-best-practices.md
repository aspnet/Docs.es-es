---
title: Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core
author: pranavkm
description: Sugerencias para aumentar el rendimiento de las aplicaciones Blazor WebAssembly en ASP.NET Core y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: ea3f197e5bab82f4fb40238fe31cd5ce29ab62ad
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900978"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Procedimientos recomendados de rendimiento de Blazor WebAssembly en ASP.NET Core

Por [Pranav Krishnamoorthy](https://github.com/pranavkm) y [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly se ha diseñado y optimizado cuidadosamente para permitir un alto rendimiento en los escenarios de interfaz de usuario de aplicaciones más realistas. Sin embargo, la generación de los mejores resultados depende de que los desarrolladores utilicen los patrones y características correctos. Considere los aspectos siguientes:

* **Rendimiento del entorno de ejecución**: el código de .NET se ejecuta en un intérprete dentro del entorno de ejecución de WebAssembly, por lo que el rendimiento de la CPU es limitado. En escenarios exigentes, la aplicación se beneficia de la [optimización de la velocidad de representación](#optimize-rendering-speed).
* **Tiempo de inicio**: la aplicación transfiere un entorno de ejecución de .NET al explorador; por ello, es importante usar características que [minimizan el tamaño de descarga de la aplicación](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Optimización de la velocidad de representación

En las secciones siguientes se proporcionan recomendaciones para minimizar la carga de trabajo de representación y mejorar la capacidad de respuesta de la IU. Si sigue este consejo, podría *multiplicar por diez o más* las velocidades de representación de la interfaz de usuario.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Evitación de la representación innecesaria de subárboles de componentes

En el entorno de ejecución, los componentes existen como una jerarquía. Un componente raíz tiene componentes secundarios. A su vez, los elementos secundarios de la raíz tienen sus propios componentes secundarios, etc. Cuando se produce un evento, como la selección de un botón por un usuario, este es el modo en que Blazor decide qué componentes se representarán:

 1. El propio evento se envía a cualquier componente que represente el controlador del evento. Después de ejecutar el controlador de eventos, ese componente se volverá a representar.
 1. Siempre que se vuelve a representar cualquier componente, este proporciona una nueva copia de los valores de los parámetros a cada uno de sus componentes secundarios.
 1. Al recibir un nuevo conjunto de valores de parámetros, cada componente elige si desea volver a representarlo. De forma predeterminada, los componentes se vuelven a representar si los valores de los parámetros pueden haber cambiado (por ejemplo, si son objetos mutables).

Los dos últimos pasos de esta secuencia continúan de forma recursiva hacia abajo en la jerarquía de componentes. En muchos casos, se volverá a representar el subárbol completo. Esto significa que los eventos que tienen como destino componentes de alto nivel pueden provocar procesos de nueva representación costosos, ya que todo lo que se encuentra por debajo de ese punto debe volver a representarse.

Si desea interrumpir este proceso y evitar la representación de la recursividad en un subárbol en particular, puede hacer lo siguiente:

 * Asegúrese de que todos los parámetros de un determinado componente son de tipos primitivos inmutables (por ejemplo, `string`, `int`, `bool`, `DateTime` y otros). La lógica integrada para detectar cambios omite automáticamente la nueva representación si ninguno de estos valores de los parámetros ha cambiado. Si representa un componente secundario con `<Customer CustomerId="@item.CustomerId" />`, donde `CustomerId` es un valor de `int`, no se volverá a representar excepto cuando `item.CustomerId` cambie.
 * Si necesita aceptar valores de parámetros no primitivos (por ejemplo, tipos de modelos personalizados), devoluciones de llamada de eventos o valores <xref:Microsoft.AspNetCore.Components.RenderFragment>, puede invalidar <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para controlar la decisión sobre si se va a representar, lo que se describe en la sección [Uso de `ShouldRender`](#use-of-shouldrender).

Al omitir la nueva representación de subárboles completos, es posible que pueda suprimir la gran mayoría de los costos de representación cuando se produce un evento.

Es posible que desee separar componentes secundarios específicamente para poder omitir la nueva representación de esa parte de la interfaz de usuario. Este es un método valido para reducir el costo de representación de un componente primario.

#### <a name="use-of-shouldrender"></a>Uso de `ShouldRender`

Si crea un componente de solo interfaz de usuario que nunca cambia tras la representación inicial (con independencia de cualquier valor de parámetros), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para que devuelva `false`:

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Si el componente solo requiere volver a representar cuando los valores de sus parámetros cambian de manera particular, puede usar campos privados para realizar el seguimiento de la información necesaria para detectar cambios. En el ejemplo siguiente, `shouldRender` se basa en la comprobación de cualquier tipo de cambio o mutación que deba provocar una nueva representación. `prevOutboundFlightId` y `prevInboundFlightId` supervisan la información de la siguiente actualización posible:

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

En el código anterior, un controlador de eventos también puede establecer `shouldRender` en `true` para que el componente se vuelva a representar después del evento.

Para la mayoría de los componentes, este nivel de control manual no es necesario. Solo debe preocuparse por omitir los subárboles de representación si esos subárboles tienen una representación especialmente costosa y están causando un retraso en la IU.

Para más información, consulte <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Virtualización

Al representar grandes cantidades de interfaz de usuario dentro de un bucle, por ejemplo, una lista o una cuadrícula con miles de entradas, la gran cantidad de operaciones de representación puede provocar un retraso en la representación de la interfaz de usuario y, por tanto, una mala experiencia del usuario. Dado que el usuario solo puede ver un pequeño número de elementos a la vez sin desplazarse, parece que no vale la pena dedicar tanto tiempo a representar elementos que no son visibles actualmente.

Para solucionar esto, Blazor proporciona un [componente `<Virtualize>`](xref:blazor/components/virtualization) integrado que crea la apariencia y los comportamientos de desplazamiento de una lista arbitrariamente grande, pero en realidad solo representa los elementos de la lista que están dentro de la ventanilla de desplazamiento actual. Por ejemplo, esto significa que la aplicación puede tener una lista con 100 000 entradas, pero solo debe pagar el costo de representación de 20 elementos que son visibles a la vez. El uso del componente `<Virtualize>` puede escalar verticalmente el rendimiento de la interfaz de usuario en órdenes de magnitud.

`<Virtualize>` se puede usar en estos casos:

 * La representación de un conjunto de elementos de datos en un bucle.
 * La mayoría de los elementos no están visibles debido al desplazamiento.
 * Los elementos representados tienen exactamente el mismo tamaño. Cuando el usuario se desplaza a un punto arbitrario, el componente puede calcular los elementos visibles que se van a mostrar.

A continuación se muestra un ejemplo de una lista no virtualizada:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Si la colección `allFlights` contiene 10 000 elementos, crea instancias y representa 10 000 instancias de componentes `<FlightSummary>`. En comparación, el ejemplo siguiente muestra una lista virtualizada:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Aunque la interfaz de usuario resultante tiene el mismo aspecto para un usuario, en segundo plano el componente solo crea instancias y representa tantas instancias de `<FlightSummary>` como se requieran para rellenar la región desplazable. El conjunto de instancias de `<FlightSummary>` que se muestran se vuelve a calcular y se representa cuando el usuario se desplaza.

`<Virtualize>` también tiene otras ventajas. Por ejemplo, cuando un componente solicita datos de una API externa, `<Virtualize>` permite que el componente capture solo el segmento de registros que corresponde a la región visible actual, en lugar de descargar todos los datos de la colección.

Para más información, consulte <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Creación de componentes ligeros y optimizados

La mayoría de los componentes de Blazor no requieren esfuerzos de optimización agresivos. Esto se debe a que la mayoría de los componentes no se repiten a menudo en la interfaz de usuario y no se vuelven a representar con alta frecuencia. Por ejemplo, los componentes `@page` y los componentes que representan partes de la interfaz de usuario de alto nivel como cuadros de diálogo o formularios, lo más probable es que solo aparezcan de uno en uno y que solo se vuelvan a representar en respuesta a un gesto del usuario. Estos componentes no suponen una gran carga de trabajo de representación, por lo que puede usar libremente cualquier combinación de características del marco que desee sin preocuparse demasiado por el rendimiento de la representación.

Sin embargo, también existen escenarios comunes en los que se compilan componentes que deben repetirse a gran escala. Por ejemplo:

 * Los formularios anidados grandes pueden tener cientos de entradas individuales, etiquetas y otros elementos.
 * Las cuadrículas pueden tener miles de celdas.
 * Los gráficos de dispersión pueden tener millones de puntos de datos.

Si modela cada unidad como instancias de componente independientes, habrá tantas que el rendimiento de su representación se vuelve crítico. En esta sección se proporcionan consejos sobre cómo crear estos componentes ligeros para que la interfaz de usuario se mantenga rápida y con capacidad de respuesta.

#### <a name="avoid-thousands-of-component-instances"></a>Evitación de miles de instancias de componentes

Cada componente es una isla independiente que se puede representar independientemente de sus elementos primarios y secundarios. Al elegir cómo dividir la interfaz de usuario en una jerarquía de componentes, toma el control sobre el nivel de detalle de la representación de la interfaz de usuario. Esto puede ser bueno o malo para el rendimiento.

 * Al dividir la interfaz de usuario en más componentes, puede ocurrir que se vuelvan a representar partes más pequeñas de la interfaz de usuario cuando se produzcan eventos. Por ejemplo, cuando un usuario hace clic en un botón en una fila de la tabla, es posible que solo pueda volver a representar esa fila en lugar de toda la página o tabla.
 * Sin embargo, cada componente adicional implica algo de memoria adicional y sobrecarga de CPU para tratar con su estado independiente y su ciclo de vida de representación.

Al optimizar el rendimiento de Blazor WebAssembly en .NET 5, se midió una sobrecarga de representación de alrededor de 0,06 ms por instancia de componente. Esto se basa en un componente simple que acepta tres parámetros que se ejecutan en un equipo portátil típico. Internamente, la sobrecarga se debe en gran parte a la recuperación del estado de cada componente de los diccionarios y al paso y recepción de parámetros. Mediante la multiplicación, puede ver que agregar 2000 instancias de componentes adicionales agregaría 0,12 segundos al tiempo de representación y la interfaz de usuario comenzaría a ser lenta para los usuarios.

Es posible hacer que los componentes sean más ligeros para que pueda tener mayor cantidad, pero con frecuencia resulta más eficaz no tener tantos componentes. Estas dos opciones se describen en las secciones siguientes.

##### <a name="inline-child-components-into-their-parents"></a>Componentes secundarios insertados en sus elementos primarios

Piense en el siguiente componente que representa una secuencia de componentes secundarios:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

En el código de ejemplo anterior, el componente `<ChatMessageDisplay>` se define en un archivo `ChatMessageDisplay.razor` que contiene:

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

El ejemplo anterior funciona bien y se ejecuta correctamente siempre y cuando no se muestren miles de mensajes a la vez. Para mostrar miles de mensajes a la vez, considere la posibilidad de *no* separar el componente `ChatMessageDisplay` independiente. En su lugar, inserte la representación directamente en el elemento primario:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

De este modo, se evita la sobrecarga por componente de la representación de tantos componentes secundarios a costa de no poder volver a representarlos de forma independiente.

##### <a name="define-reusable-renderfragments-in-code"></a>Definición de `RenderFragments` reutilizables en el código

Puede que esté separando los componentes secundarios únicamente como una manera de reutilizar la lógica de representación. En ese caso, todavía es posible reutilizar la lógica de representación sin declarar componentes reales. En el bloque `@code` de cualquier componente, puede definir un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment> que emita la interfaz de usuario y al que se pueda llamar desde cualquier lugar:

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Como se demostró en el ejemplo anterior, los componentes pueden emitir marcado a partir del código dentro de su bloque `@code` y fuera de él. Este enfoque define un delegado <xref:Microsoft.AspNetCore.Components.RenderFragment> que puede representar dentro de la salida de representación normal del componente, opcionalmente en varios lugares. Es necesario que el delegado acepte un parámetro denominado `__builder` de tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para que el compilador de Razor pueda generar instrucciones de representación para él.

Si desea que sea reutilizable en varios componentes, considere la posibilidad de declararlo miembro de `public static`:

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Ahora se le podría invocar desde un componente no relacionado. Esta técnica resulta útil para crear bibliotecas de fragmentos de marcado reutilizables que se representan sin sobrecarga por componente.

Los delegados de <xref:Microsoft.AspNetCore.Components.RenderFragment> también pueden aceptar parámetros. Para crear el equivalente del componente `ChatMessageDisplay` del ejemplo anterior:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Este enfoque proporciona la ventaja de reutilizar la lógica de representación sin la sobrecarga por componente. Sin embargo, no tiene la ventaja de poder actualizar su subárbol de la interfaz de usuario de forma independiente, ni tampoco tiene la capacidad de omitir la representación de ese subárbol de la interfaz de usuario cuando su elemento primario se representa, ya que no hay ningún límite de componente.

#### <a name="dont-receive-too-many-parameters"></a>No recepción de demasiados parámetros

Si un componente se repite con mucha frecuencia, por ejemplo, cientos o miles de veces, tenga en cuenta que la sobrecarga de pasar y recibir cada parámetro aumenta.

Es raro que demasiados parámetros restrinjan gravemente el rendimiento, pero puede ser un factor. En el caso de un componente `<TableCell>` que se represente 1000 veces dentro de una cuadrícula, cada parámetro adicional que se le pase podría agregar alrededor de 15 ms al costo total de representación. Si cada celda aceptara 10 parámetros, el paso de parámetros tomaría alrededor de 150 ms por representación de componente y, por lo tanto, quizás 150 000 ms (150 segundos), lo que por sí mismo causaría un retraso en la interfaz de usuario.

Para reducir esta carga, puede agrupar varios parámetros a través de clases personalizadas. Por ejemplo, un componente `<TableCell>` podría aceptar:

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

En el ejemplo anterior, `Data` es diferente para cada celda, pero `Options` es común en todas ellas. Por supuesto, podría ser una mejora no tener un componente `<TableCell>` y, en su lugar, incorporar su lógica en el componente principal.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Garantía de que los parámetros en cascada son fijos

El componente `<CascadingValue>` tiene un parámetro opcional denominado `IsFixed`.

 * Si el valor `IsFixed` es `false` (el valor predeterminado), cada destinatario del valor en cascada configura una suscripción para recibir notificaciones de los cambios. En este caso, cada `[CascadingParameter]` es **sustancialmente más caro** que un `[Parameter]` normal debido al seguimiento de las suscripciones.
 * Si el valor `IsFixed` es `true` (por ejemplo, `<CascadingValue Value="@someValue" IsFixed="true">`), los destinatarios reciben el valor inicial, pero *no* configuran ninguna suscripción para recibir actualizaciones. En este caso, cada `[CascadingParameter]` es ligero y **no es más caro** que un `[Parameter]` normal.

Por tanto, siempre que sea posible, debe usar `IsFixed="true"` en valores en cascada. Puede hacerlo cada vez que el valor proporcionado no cambie con el tiempo. En el patrón común en el que un componente pasa `this` como un valor en cascada, debe utilizar `IsFixed="true"`:

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Esto supone una gran diferencia si hay un gran número de componentes diferentes que reciben el valor en cascada. Para más información, consulte <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Evitación de la expansión de atributos con `CaptureUnmatchedValues`

Los componentes pueden optar por recibir valores de parámetro "no coincidentes" mediante la marca <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>:

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Este enfoque permite pasar atributos adicionales arbitrarios al elemento. Sin embargo, también es bastante costoso porque el representador debe:

* Hacer coincidir todos los parámetros proporcionados con el conjunto de parámetros conocidos para compilar un diccionario.
* Supervisar el modo en que varias copias del mismo atributo se sobrescriben entre sí.

No dude en usar <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en componentes que no sean críticos para el rendimiento, como aquellos que no se repiten con frecuencia. Sin embargo, para los componentes que se representan a escala, como cada elemento en una lista extensa o las celdas de una cuadrícula, intente evitar la expansión de atributos.

Para más información, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>Implementación manual de `SetParametersAsync`

Uno de los principales aspectos de la sobrecarga de representación por componente es escribir valores de parámetros entrantes en las propiedades de `[Parameter]`. El representador tiene que usar la reflexión para hacerlo. Aunque esto está optimizado en cierto modo, la ausencia de compatibilidad con JIT en el entorno de ejecución de WebAssembly impone límites.

En algunos casos extremos, puede que desee evitar la reflexión e implementar su propia lógica de configuración de parámetros de forma manual. Esto puede ser aplicable cuando:

 * Tiene un componente que se representa con mucha frecuencia (por ejemplo, hay cientos o miles de copias suyas en la interfaz de usuario).
 * Acepta muchos parámetros.
 * Observa que la sobrecarga de recibir parámetros tiene una incidencia observable en la capacidad de respuesta de la interfaz de usuario.

En estos casos, puede invalidar el método <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> virtual del componente e implementar su propia lógica específica del componente. En el ejemplo siguiente se evita deliberadamente cualquier búsqueda en el diccionario:

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

En el código anterior, la devolución de la clase base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ejecuta los métodos de ciclo de vida normales sin asignar los parámetros de nuevo.

Como puede ver en el código anterior, invalidar <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> y proporcionar lógica personalizada es complicado y laborioso, por lo que no se recomienda este enfoque en general. En casos extremos, puede mejorar el rendimiento de la representación en un 20-25 %, pero solo debe considerar este enfoque en los escenarios mencionados anteriormente.

### <a name="dont-trigger-events-too-rapidly"></a>No desencadenación de eventos demasiado rápido

Algunos eventos del navegador se activan con mucha frecuencia, por ejemplo, `onmousemove` y `onscroll`, que pueden activarse decenas o cientos de veces por segundo. En la mayoría de los casos, no es necesario realizar actualizaciones de la interfaz de usuario con frecuencia. Si intenta hacerlo, puede dañar la capacidad de respuesta de la interfaz de usuario o consumir un tiempo excesivo de CPU.

En lugar de usar eventos `@onmousemove` o `@onscroll` nativos, es posible que prefiera usar la interoperabilidad de JS para registrar una devolución de llamada que se active con menos frecuencia. Por ejemplo, el siguiente componente (`MyComponent.razor`) muestra la posición del mouse, pero solo se actualiza como máximo una vez cada 500 ms:

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

El código de JavaScript correspondiente, que puede colocarse en la página `index.html` o cargarse como un módulo ES6, registra el cliente de escucha de eventos DOM real. En este ejemplo, el cliente de escucha de eventos usa la [función `throttle` de Lodash](https://lodash.com/docs/4.17.15#throttle) para limitar la velocidad de las invocaciones:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Esta técnica puede ser aún más importante para Blazor Server, ya que cada invocación de eventos implica la entrega de un mensaje a través de la red. Es útil para Blazor WebAssembly porque puede reducir en gran medida la cantidad de trabajo de representación.

## <a name="optimize-javascript-interop-speed"></a>Optimización de la velocidad de interoperabilidad de JavaScript

Las llamadas entre .NET y JavaScript implican una sobrecarga adicional porque:

 * De forma predeterminada, las llamadas son asincrónicas.
 * De forma predeterminada, los parámetros y los valores devueltos se serializan en JSON. Esto es para proporcionar un mecanismo de conversión fácil de comprender entre los tipos de .NET y JavaScript.

Además, en Blazor Server, estas llamadas se pasan a través de la red.

### <a name="avoid-excessively-fine-grained-calls"></a>Evitación de llamadas excesivamente detalladas

Puesto que cada llamada implica cierta sobrecarga, puede ser útil reducir el número de llamadas. Considere el siguiente código, que almacena una colección de elementos en el almacén `localStorage` del explorador:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

En el ejemplo anterior se realiza una llamada de interoperabilidad de JS independiente para cada elemento. En su lugar, el enfoque siguiente reduce la interoperabilidad de JS a una sola llamada:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

La función de JavaScript correspondiente se define de la siguiente manera:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

Para Blazor WebAssembly, esto normalmente solo es importante si va a realizar un gran número de llamadas de interoperabilidad de JS.

### <a name="consider-making-synchronous-calls"></a>Consideración de la posibilidad de realizar llamadas sincrónicas

De forma predeterminada, las llamadas de interoperabilidad de JavaScript son asincrónicas, independientemente de si el código al que se llama es sincrónico o asincrónico. El motivo es asegurarse de que los componentes son compatibles con Blazor WebAssembly y Blazor Server. En Blazor Server, todas las llamadas de interoperabilidad de JavaScript deben ser asincrónicas porque se envían a través de una conexión de red.

Si sabe con certeza que la aplicación solo se ejecuta en Blazor WebAssembly, puede optar por realizar llamadas de interoperabilidad de JavaScript sincrónicas. Esto tiene una sobrecarga ligeramente menor que la realización de llamadas asincrónicas y puede dar lugar a menos ciclos de representación porque no hay ningún estado intermedio mientras se esperan los resultados.

Para hacer una llamada sincrónica de .NET a JavaScript, convierta <xref:Microsoft.JSInterop.IJSRuntime> en <xref:Microsoft.JSInterop.IJSInProcessRuntime>:

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

Al trabajar con `IJSObjectReference`, puede realizar una llamada sincrónica convirtiéndola en `IJSInProcessObjectReference`.

::: moniker-end

Para hacer una llamada sincrónica desde JavaScript a .NET, use `DotNet.invokeMethod` en lugar de `DotNet.invokeMethodAsync`.

Las llamadas sincrónicas funcionan si:

* La aplicación se ejecuta en Blazor WebAssembly, no en Blazor Server.
* La función a la que se ha llamado devuelve un valor sincrónicamente (no es un método `async` y no devuelve un valor <xref:System.Threading.Tasks.Task>de .NET o `Promise` de JavaScript).

Para más información, consulte <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Consideración de la posibilidad de realizar llamadas deserializadas

Cuando se ejecuta en Blazor WebAssembly, es posible realizar llamadas deserializadas de .NET a JavaScript. Se trata de llamadas sincrónicas que no realizan la serialización de JSON de argumentos o valores devueltos. Todos los aspectos de la administración de la memoria y las traslaciones entre las representaciones de .NET y JavaScript quedan en manos del desarrollador.

> [!WARNING]
> Aunque el uso de `IJSUnmarshalledRuntime` es el método de interoperabilidad de JavaScript con menor sobrecarga, las API de JavaScript necesarias para interactuar con estas API no están documentadas en este momento y están sujetas a cambios importantes en versiones futuras.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Minimización del tamaño de descarga de la aplicación

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Recorte de lenguaje intermedio (IL)

[Cuando una aplicación Blazor WebAssembly se recorta](xref:blazor/host-and-deploy/configure-trimmer), el tamaño de la aplicación se reduce quitando el código que no se usa en los archivos binarios de la aplicación. De forma predeterminada, el recortador se ejecuta al publicar una aplicación. Para sacar partido del recorte, publique la aplicación de implementación mediante el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Vinculación de lenguaje intermedio

[Cuando una aplicación Blazor WebAssembly se vincula](xref:blazor/host-and-deploy/configure-linker), el tamaño de la aplicación se reduce recortando el código que no se usa en los archivos binarios de la aplicación. De forma predeterminada, el enlazador de lenguaje intermedio (IL) solo está habilitado cuando se compila en la configuración de `Release`. Para sacar partido de esto, publique la aplicación de implementación con el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish), con la opción [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release`:

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Uso de System.Text.Json

La implementación de interoperabilidad de JavaScript de Blazor se basa en <xref:System.Text.Json>, que es una biblioteca de serialización de JSON de alto rendimiento con una asignación de memoria reducida. El uso de <xref:System.Text.Json> no da como resultado un mayor tamaño de carga de aplicación frente a la adición de una o varias bibliotecas JSON alternativas.

Para obtener instrucciones sobre la migración, vea [Procedimiento para realizar la migración de `Newtonsoft.Json` a `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Ensamblados de carga diferida

Cargue los ensamblados en tiempo de ejecución cuando una ruta los requiera. Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compresión

Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución. Blazor se basa en el servidor para realizar negociación de contenido y proporcionar archivos comprimidos estáticamente.

Cuando una aplicación se haya implementado, compruebe que proporciona archivos comprimidos. Inspeccione la pestaña Red de las herramientas de desarrollo del explorador y compruebe que los archivos se proporcionan con `Content-Encoding: br` o `Content-Encoding: gz`. Si el host no proporciona archivos comprimidos, siga las instrucciones de <xref:blazor/host-and-deploy/webassembly#compression>.

### <a name="disable-unused-features"></a>Deshabilitar las características sin usar

El runtime de Blazor WebAssembly incluye las siguientes características de .NET, que se pueden deshabilitar si la aplicación no las necesita cuando el tamaño de la carga útil es más pequeño:

* Se incluye un archivo de datos para que la información de zona horaria sea correcta. Si la aplicación no necesita esta característica, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorEnableTimeZoneSupport` del archivo de proyecto de la aplicación:

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* De forma predeterminada, Blazor WebAssembly incluye los recursos de globalización necesarios para mostrar valores, como las fechas y la moneda, en la referencia cultural del usuario. Si la aplicación no requiere localización, es posible [configurar la aplicación para que admita la referencia cultural invariable](xref:blazor/globalization-localization), que se basa en la referencia cultural `en-US`:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Se incluye información de intercalación para que API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente. Si está convencido de que la aplicación no necesita datos de intercalación, considere la posibilidad de deshabilitarla estableciendo en `false` la propiedad de MSBuild `BlazorWebAssemblyPreserveCollationData` del archivo de proyecto de la aplicación:

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
