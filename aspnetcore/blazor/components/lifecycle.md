---
title: Ciclo de vida de ASP.NET Core Blazor
author: guardrex
description: Aprenda a usar los métodos de ciclo de vida de los componentes de Razor en aplicaciones de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: e3abfd0535bc10867c9b5f980bb5439cc918dfab
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712329"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a>Ciclo de vida de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos. Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="before-parameters-are-set"></a>Antes de establecer los parámetros

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> contiene el conjunto de valores de parámetros del componente cada vez que se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.

La implementación predeterminada de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece el valor de cada propiedad con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), que tiene un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView>. Los parámetros que no tienen un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView> se dejan sin cambios.

Si no se invoca [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A), el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria. Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.

Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación. Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).

### <a name="component-initialization-methods"></a>Métodos de inicialización de componentes

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario en <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>. 

Utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.

En el caso de una operación sincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Para realizar una operación asincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y use el operador [`await`](/dotnet/csharp/language-reference/operators/await) en la operación:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/fundamentals/additional-scenarios#render-mode) llaman a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dos veces_**:

* Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.
* Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.

Para evitar que el código de desarrollador en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).

Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador. Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente. Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).

Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación. Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-parameters-are-set"></a>Después de establecer los parámetros

Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:

* Después de inicializar el componente en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
* Cuando el componente primario vuelve a representarse y proporciona lo siguiente:
  * Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.
  * Cualquier parámetro de tipo complejo. El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación. Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-component-render"></a>Después de representar el componente

Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación. En este momento, se rellenan las referencias a elementos y componentes. Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.

El parámetro `firstRender` para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:

* Se establece en `true` la primera vez que se representa la instancia del componente.
* Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.
>
> Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa. Esto se hace para evitar un bucle de representación infinito. Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

*No se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> al representarse previamente en el servidor.*

Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación. Para obtener más información, vea la sección [Eliminación de componentes con `IDisposable`](#component-disposal-with-idisposable).

### <a name="suppress-ui-refreshing"></a>Supresión de la actualización de la interfaz de usuario

Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario. Si la implementación devuelve `true`, la interfaz de usuario se actualiza:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> cada vez que se representa el componente.

Aunque se invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, el componente siempre se representa inicialmente.

Para obtener más información, vea <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.

## <a name="state-changes"></a>Cambios de estado

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado. Cuando es aplicable, la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> hace que el componente se represente.

A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se le llama automáticamente para métodos <xref:Microsoft.AspNetCore.Components.EventCallback>. Para obtener más información, vea <xref:blazor/components/event-handling#eventcallback>.

## <a name="handle-incomplete-async-actions-at-render"></a>Control de acciones asincrónicas incompletas en la representación

Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente. Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida. Proporcione lógica de representación para confirmar que los objetos se inicializan. Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.

En el componente `FetchData` de las plantillas de Blazor, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se invalida para recibir de forma asincrónica datos de previsión (`forecasts`). Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga. Una vez que se completa el elemento `Task` que devuelve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, el componente se representará con el estado actualizado.

`Pages/FetchData.razor` en la plantilla de Blazor Server:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a>Control de errores

Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Reconexión con estado después de la representación previa

En una aplicación Blazor Server, cuando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> es <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, el componente se representa inicialmente de forma estática como parte de la página. Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo. Si el método de ciclo de vida [`OnInitialized{Async}`](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:

* Cuando el componente se representa previamente de forma estática.
* Después de establecerse la conexión con el servidor.

Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.

Para evitar el escenario de representación doble en una aplicación Blazor Server:

* Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.
* Use el identificador durante la representación previa para guardar el estado del componente.
* Use el identificador después de la representación previa para recuperar el estado copiado en caché.

En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Para obtener más información sobre <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, vea <xref:blazor/fundamentals/additional-scenarios#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Detección de cuándo se está obteniendo una representación previa de la aplicación

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Eliminación de componentes con IDisposable

Si un componente implementa <xref:System.IDisposable>, se llama al [método `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario. La eliminación puede realizarse en cualquier momento, incluso durante la [inicialización de componentes](#component-initialization-methods). El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.

Cancele la suscripción de los controladores de eventos de .NET. En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:

* Enfoque de campo privado y expresión lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Enfoque de método privado

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a>Trabajo en segundo plano cancelable

Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos. Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones. Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.

Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:

* Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.
* El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.
* La prioridad de las tareas que se están ejecutando debe cambiarse.
* La aplicación debe apagarse para volver a implementarla en el servidor.
* Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.

Para implementar un patrón de trabajo en segundo plano cancelable en un componente:

* Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.
* Durante la [eliminación del componente](#component-disposal-with-idisposable), y en cualquier momento en el que interese realizar la cancelación mediante la cancelación manual del token, llame a [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para indicar que se debe cancelar el trabajo en segundo plano.
* Cuando se devuelva la llamada asincrónica, llame a <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> en el token.

En el ejemplo siguiente:

* `await Task.Delay(5000, cts.Token);` representa el trabajo asincrónico en segundo plano de ejecución prolongada.
* `BackgroundResourceMethod` representa un método en segundo plano de ejecución prolongada que no debe iniciarse si se elimina `Resource` antes de llamar al método.

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
