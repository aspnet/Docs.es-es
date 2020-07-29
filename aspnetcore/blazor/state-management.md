---
title: Administración de estado de [Blazor en ASP.NET Core
author: guardrex
description: Aprenda a conservar el estado en las aplicaciones [Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/state-management
ms.openlocfilehash: a6c646425145855538f408ec6cafdb151cd24b86
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401953"
---
# <a name="aspnet-core-blazor-state-management"></a>Administración de estado de [Blazor en ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

El servidor [Blazor Server es un marco para aplicaciones con estado. La mayoría de las veces, la aplicación mantiene una conexión continua con el servidor. El estado del usuario se mantiene en la memoria del servidor en un *circuito*. 

Entre los ejemplos de estado que se mantiene para el circuito de un usuario se incluyen:

* La interfaz de usuario representada: la jerarquía de instancias de componente y su salida de representación más reciente.
* Los valores de los campos y las propiedades de las instancias del componente.
* Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.

> [!NOTE]
> En este artículo se aborda la persistencia de estado en aplicaciones [Blazor Server. Las aplicaciones [Blazor WebAssembly pueden aprovechar las ventajas de la [persistencia de estado del lado cliente en el explorador](#client-side-in-the-browser), pero requieren soluciones personalizadas o paquetes de terceros más allá del ámbito de este artículo.

## <a name="blazor-circuits"></a>Circuitos de [Blazor

Si un usuario experimenta una pérdida de conexión de red temporal, [Blazor intenta volver a conectar al usuario con su circuito original para poder seguir usando la aplicación. Sin embargo, no siempre es posible volver a conectar a un usuario con su circuito original en la memoria del servidor:

* El servidor no puede mantener un circuito desconectado para siempre. El servidor debe liberar un circuito desconectado después de un tiempo de espera o cuando el servidor esté bajo presión de memoria.
* En entornos de implementación multiservidor y de carga equilibrada, las solicitudes de procesamiento de servidor pueden dejar de estar disponibles en un momento dado. Se puede producir un error en los servidores individuales o estos se pueden quitar automáticamente cuando ya no sean necesarios para controlar el volumen total de solicitudes. Es posible que el servidor original no esté disponible cuando el usuario intente volver a conectarse.
* El usuario podría cerrar y volver a abrir el explorador o recargar la página, lo que elimina cualquier estado que se mantenga en la memoria del explorador. Por ejemplo, los valores establecidos mediante llamadas de interoperabilidad de JavaScript se pierden.

Cuando un usuario no se puede volver a conectar a su circuito original, el usuario recibe un circuito nuevo con un estado vacío. Esto es equivalente a cerrar y volver a abrir una aplicación de escritorio.

## <a name="preserve-state-across-circuits"></a>Conservar el estado entre circuitos

En algunos escenarios, es deseable conservar el estado entre los circuitos. Una aplicación puede conservar datos importantes para un usuario si:

* El servidor web deja de estar disponible.
* El explorador del usuario se ve obligado a iniciar un nuevo circuito con un nuevo servidor web.

En general, mantener el estado en los circuitos se aplica a los escenarios en los que los usuarios crean activamente datos, no simplemente leyendo los datos que ya existen.

Para conservar el estado más allá de un solo circuito, *no almacene simplemente los datos en la memoria del servidor*. La aplicación debe conservar los datos en otra ubicación de almacenamiento. La persistencia del estado no es automática. Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.

La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios han gastado en crear. En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:

* Formulario web de varios pasos: durante un proceso que consta de varios pasos, en caso de que la información se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado. Un usuario pierde el estado en este escenario si se sale del formulario de varios pasos y vuelve al formulario más adelante.
* Carro de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales. Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.

Normalmente no es necesario conservar el estado de fácil creación, como el nombre de usuario especificado en un cuadro de diálogo de inicio de sesión que no se ha enviado.

> [!IMPORTANT]
> Una aplicación solo puede conservar el *estado de la aplicación*. Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación. Los componentes y los árboles de representación no suelen ser serializables. Para conservar algo similar al estado de la interfaz de usuario, como los nodos expandidos de una vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento como estado de la aplicación serializable.

## <a name="where-to-persist-state"></a>Dónde conservar el estado

Existen tres ubicaciones comunes para el estado persistente en una aplicación [Blazor Server. Cada enfoque es más adecuado para distintos escenarios y tiene advertencias diferentes:

* [Del lado servidor en una base de datos](#server-side-in-a-database)
* [URL](#url)
* [Del lado cliente en el explorador](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Del lado servidor en una base de datos

Para la persistencia de datos permanente o para cualquier dato que deba abarcar varios usuarios o dispositivos, una base de datos independiente en el servidor es casi seguro la mejor opción. Las opciones son:

* Base de datos SQL relacional
* almacén de pares clave-valor
* Almacén de blobs
* Almacén de tablas

Una vez guardados los datos en la base de datos, un usuario puede iniciar un nuevo circuito en cualquier momento. Los datos del usuario se conservan y están disponibles en cualquier circuito nuevo.

Para más información sobre las opciones de almacenamiento de datos de Azure, consulte la [documentación de Azure Storage](/azure/storage/) y las [bases de datos de Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>Resolución

Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL. Entre los ejemplos de estado modelado en la dirección URL se incluyen:

* El identificador de una entidad visualizada.
* El número de la página actual en una cuadrícula paginada.

Se conserva el contenido de la barra de direcciones del explorador:

* Si el usuario recarga manualmente la página.
* Si el servidor web deja de estar disponible y el usuario se ve obligado a volver a cargar la página para conectarse a un servidor diferente.

Para obtener información sobre cómo definir patrones de direcciones URL con la directiva `@page`, vea <xref:blazor/fundamentals/routing>.

### <a name="client-side-in-the-browser"></a>Del lado cliente en el explorador

En el caso de los datos transitorios que el usuario está creando activamente, una memoria auxiliar común son las colecciones `localStorage` y `sessionStorage` del explorador. No es necesario que la aplicación administre o borre el estado almacenado si se abandona el circuito, lo que supone una ventaja sobre el almacenamiento del lado servidor.

> [!NOTE]
> "Del lado cliente" en esta sección se refiere a los escenarios del lado cliente en el explorador, no al [modelo de hospedaje de [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly). `localStorage` y `sessionStorage` se pueden usar en aplicaciones [Blazor WebAssembly, pero solo escribiendo código personalizado o usando un paquete de terceros.

`localStorage` y `sessionStorage` difieren de la siguiente manera:

* `localStorage` está en el ámbito del explorador del usuario. Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva. Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas. Los datos se conservan en `localStorage` hasta que se borran explícitamente.
* `sessionStorage` está en el ámbito de la pestaña del explorador del usuario. Si el usuario recarga la pestaña, el estado se conserva. Si el usuario cierra la pestaña o el explorador, se pierde el estado. Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.

Por lo general, es más seguro usar `sessionStorage`. `sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:

* Errores en el almacenamiento de estado en todas las pestañas.
* Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.

`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.

Advertencias sobre el uso del almacenamiento del explorador:

* De forma similar al uso de una base de datos del lado servidor, la carga y el almacenamiento de datos son asincrónicos.
* A diferencia de las bases de datos del lado servidor, el almacenamiento no está disponible durante la representación previa porque la página solicitada no existe en el explorador durante la fase de representación previa.
* Es razonable almacenar unos pocos kilobytes de datos para las aplicaciones [Blazor Server. Más allá de unos pocos kilobytes, debe tener en cuenta las implicaciones de rendimiento porque los datos se cargan y se guardan en la red.
* Los usuarios pueden ver o alterar los datos. La [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core puede mitigar el riesgo.

## <a name="third-party-browser-storage-solutions"></a>Soluciones de almacenamiento de explorador de terceros

Los paquetes NuGet de terceros proporcionan API para trabajar con `localStorage` y `sessionStorage`.

Merece la pena tener en cuenta la elección de un paquete que utiliza de forma transparente la [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core. La protección de datos de ASP.NET Core cifra los datos almacenados y reduce el riesgo potencial de alteración de los datos almacenados. Si los datos serializados con JSON se almacenan en texto sin formato, los usuarios pueden ver los datos mediante las herramientas de desarrollo del explorador y también modificar los datos almacenados. La protección de datos no siempre es un problema, ya que los datos pueden ser bastante triviales. Por ejemplo, la lectura o modificación del color almacenado de un elemento de la interfaz de usuario no supone un riesgo de seguridad significativo para el usuario o la organización. Evite que los usuarios puedan inspeccionar o manipular *datos confidenciales*.

## <a name="protected-browser-storage-experimental-package"></a>Paquete experimental del almacenamiento de explorador protegido

Un ejemplo de paquete NuGet que proporciona [protección de datos](xref:security/data-protection/introduction) para `localStorage` y `sessionStorage` es [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` es un paquete experimental no compatible que no es adecuado para su uso en producción en este momento.

### <a name="installation"></a>Instalación

Para instalar el paquete de `Microsoft.AspNetCore.ProtectedBrowserStorage`:

1. En el proyecto de aplicación [Blazor Server, agregue una referencia de paquete a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. En el código HTML de nivel superior (por ejemplo, en el archivo `Pages/_Host.cshtml` de la plantilla de proyecto predeterminada), agregue la siguiente etiqueta de `<script>`:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. En el método `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Guardar y cargar datos dentro de un componente

En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

La elección depende de la memoria auxiliar que quiera usar. En el ejemplo siguiente, se usa `sessionStorage`:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

La instrucción `@using` se puede colocar en un archivo `_Imports.razor`, en lugar de en el componente. El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.

Para conservar el valor de `currentCount` en el componente `Counter` de la plantilla de proyecto, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable. Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo. `ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON.

En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario. Los datos no se almacenan en texto no cifrado, sino que se protegen mediante la [protección de datos](xref:security/data-protection/introduction) de ASP.NET Core. Los datos cifrados pueden verse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.

Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante (incluso si está en un circuito totalmente nuevo), use `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página. Para obtener más información, vea <xref:blazor/components/lifecycle>.

> [!WARNING]
> Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa. Con la representación previa habilitada, se genera un error similar al siguiente:
>
> > No se pueden emitir llamadas de interoperabilidad de JavaScript en este momento. Esto se debe a que el componente se está representando previamente.
>
> Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa. Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Controlar el estado de carga

Dado que el almacenamiento del explorador es asincrónico (al que se accede a través de una conexión de red), siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para que los use un componente. Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.

Un enfoque consiste en realizar un seguimiento de si los datos son `null` (aún se están cargando) o no. En el componente `Counter` predeterminado, el recuento se mantiene en `int`. Haga que `currentCount` admita valores NULL agregando un signo de interrogación (`?`) al tipo (`int`):

```csharp
private int? currentCount;
```

En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , elija que estos elementos se muestren solo si se cargan los datos:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Controlar la representación previa

Durante la representación previa:

* No existe ninguna conexión interactiva con el explorador del usuario.
* El explorador todavía no tiene ninguna página en la que se pueda ejecutar código JavaScript.

`localStorage` o `sessionStorage` no están disponibles durante la representación previa. Si el componente intenta interactuar con el almacenamiento, se genera un error similar a:

> No se pueden emitir llamadas de interoperabilidad de JavaScript en este momento. Esto se debe a que el componente se está representando previamente.

Una manera de resolver el error es deshabilitar la representación previa. Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador. La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.

Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el valor de `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.

La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`. Para mantener habilitada la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito. El siguiente es un ejemplo para almacenar un valor de contador:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factorizar la preservación de estado en una ubicación común

Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código. Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado. Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.

En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

El componente `CounterStateProvider` controla la fase de carga, ya que no representa su contenido secundario hasta que se completa la carga.

Para usar el componente `CounterStateProvider`, encapsule una instancia del componente en torno a cualquier otro componente que requiera acceso al estado del contador. Para que el estado sea accesible a todos los componentes de una aplicación, encapsule el componente `CounterStateProvider` alrededor de <xref:Microsoft.AspNetCore.Components.Routing.Router> en el componente `App` (`App.razor`):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Los componentes encapsulados reciben y pueden modificar el estado persistente del contador. El siguiente componente `Counter` implementa el patrón:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

No es necesario que el componente anterior interactúe con `ProtectedBrowserStorage`, ni se trata de una fase de carga.

Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa. Para obtener más información, consulte la sección [Controlar la representación previa](#handle-prerendering).

En general, se recomienda el patrón *componente primario del proveedor de estado*:

* Para consumir el estado en muchos otros componentes.
* Si solo hay un objeto de estado de nivel superior para conservar.

Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar el control de la carga y el guardado de estado globalmente.
