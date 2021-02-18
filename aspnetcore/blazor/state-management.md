---
title: Administración de estado de Blazor en ASP.NET Core
author: guardrex
description: Aprenda a conservar el estado en las aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 811ec08eb457fcf0697a64dc4990d29082454f73
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280857"
---
# <a name="aspnet-core-blazor-state-management"></a>Administración de estado de Blazor en ASP.NET Core

::: zone pivot="webassembly"

El estado de usuario creado en una aplicación Blazor WebAssembly se guarda en la memoria del explorador.

Entre los ejemplos de estado de usuario almacenados en la memoria del explorador se incluyen:

* La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.
* Los valores de los campos y las propiedades de las instancias del componente.
* Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).
* Los valores establecidos mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).

Cuando un usuario cierra y vuelve a abrir el explorador, o vuelve a cargar la página, se pierde el estado de usuario guardado en la memoria del explorador.

> [!NOTE]
> El [almacenamiento del explorador protegido](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (espacio de nombres <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName>) se basa en la protección de datos de ASP.NET Core y solo se admite para aplicaciones de Blazor Server.

## <a name="persist-state-across-browser-sessions"></a>Conservación del estado en todas las sesiones del explorador

En general, se mantiene el estado en las sesiones del explorador en las que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.

Para conservar el estado en todas las sesiones del explorador, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del explorador. La persistencia del estado no es automática. Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.

La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon. En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:

* Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado. Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.
* Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales. Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.

Una aplicación solo puede conservar el *estado de la aplicación*. Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación. Los componentes y los árboles de representación no suelen ser serializables. Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.

## <a name="where-to-persist-state"></a>Dónde conservar el estado

Existen ubicaciones comunes para el estado persistente:

* [Almacenamiento del lado servidor](#server-side-storage-wasm)
* [URL](#url-wasm)
* [Almacenamiento del explorador](#browser-storage-wasm)
* [Servicio de contenedor de estado en memoria](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm">Almacenamiento del lado servidor</h2>

Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor independiente al que se accede a través de una API web. Las opciones incluyen:

* Blob Storage
* Almacenamiento de pares clave-valor
* Base de datos relacional
* Almacenamiento de tablas

Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier nueva sesión del explorador.

Dado que las aplicaciones Blazor WebAssembly se ejecutan completamente en el explorador del usuario, requieren medidas adicionales para acceder a sistemas externos seguros, como bases de datos y servicios de almacenamiento. Las aplicaciones de Blazor WebAssembly se protegen de la misma manera que las aplicaciones de página única (SPA). Normalmente, una aplicación autentica a un usuario a través de [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) y, a continuación, interactúa con los servicios de almacenamiento y las bases de datos a través de llamadas de API web a una aplicación del lado servidor. La aplicación del lado servidor media en la transferencia de datos entre la aplicación Blazor WebAssembly y la base de datos o el servicio de almacenamiento. La aplicación Blazor WebAssembly mantiene una conexión efímera con la aplicación del lado servidor, mientras que la aplicación del lado servidor tiene una conexión persistente con el almacenamiento.

Para obtener más información, consulte los siguientes recursos:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Artículos sobre *seguridad e Identity* de Blazor

Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:

* [Bases de datos de Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentación de Azure Storage](/azure/storage/)

<h2 id="url-wasm">Resolución</h2>

Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL. Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:

* El identificador de una entidad visualizada.
* El número de la página actual en una cuadrícula paginada.

El contenido de la barra de direcciones del explorador se conserva si el usuario vuelve a cargar la página manualmente.

Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.

<h2 id="browser-storage-wasm">Almacenamiento del explorador</h2>

En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:

* `localStorage` está en el ámbito de la ventana del explorador. Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva. Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas. Los datos se conservan en `localStorage` hasta que se borran explícitamente.
* `sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva. Si el usuario cierra la pestaña o el explorador, se pierde el estado. Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.

> [!NOTE]
> `localStorage` y `sessionStorage` se pueden usar en aplicaciones Blazor WebAssembly, pero solo escribiendo código personalizado o usando un paquete de terceros.

Por lo general, es más seguro usar `sessionStorage`. `sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:

* Errores en el almacenamiento de estado en todas las pestañas.
* Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.

`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.

> [!WARNING]
> Los usuarios pueden ver o alterar los datos almacenados en `localStorage` y `sessionStorage`.

<h2 id="in-memory-state-container-service-wasm">Servicio de contenedor de estado en memoria</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Guardar el estado de la aplicación antes de una operación de autenticación](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

El servidor Blazor Server es un marco para aplicaciones con estado. La mayoría de las veces, la aplicación mantiene una conexión con el servidor. El estado del usuario se mantiene en la memoria del servidor en un *circuito*. 

Entre los ejemplos de estado de usuario que se mantiene en un circuito se incluyen:

* La jerarquía de instancias del componente y su salida de representación más reciente en la interfaz de usuario representada.
* Los valores de los campos y las propiedades de las instancias del componente.
* Los datos contenidos en las instancias de servicio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) que se encuentran en el ámbito del circuito.

También se puede encontrar el estado de usuario en las variables de JavaScript en el conjunto de la memoria del explorador mediante llamadas de [interoperabilidad de JavaScript](xref:blazor/call-javascript-from-dotnet).

Si un usuario experimenta una pérdida de conexión de red temporal, Blazor intenta volver a conectar al usuario a su circuito original con su estado original. Sin embargo, no siempre es posible volver a conectar a un usuario con su circuito original en la memoria del servidor:

* El servidor no puede mantener un circuito desconectado para siempre. El servidor debe liberar un circuito desconectado después de un tiempo de espera o cuando el servidor esté bajo presión de memoria.
* En entornos de implementación de carga equilibrada y multiservidor, se puede producir un error en los servidores individuales o estos se pueden quitar automáticamente cuando ya no sean necesarios para controlar el volumen total de solicitudes. Las solicitudes de procesamiento de servidor originales de un usuario pueden dejar de estar disponibles cuando el usuario intenta volver a conectarse.
* El usuario podría cerrar y volver a abrir el explorador o recargar la página, lo que elimina cualquier estado que se mantenga en la memoria del explorador. Por ejemplo, los valores de las variables de JavaScript establecidos mediante llamadas de interoperabilidad de JavaScript se pierden.

Cuando un usuario no se puede volver a conectar a su circuito original, el usuario recibe un circuito nuevo con un estado vacío. Esto es equivalente a cerrar y volver a abrir una aplicación de escritorio.

## <a name="persist-state-across-circuits"></a>Conservación del estado entre circuitos

En general, se mantiene el estado en los circuitos en los que los usuarios crean datos de forma activa, no simplemente leyendo los datos que ya existen.

Para conservar el estado en los circuitos, la aplicación debe conservar los datos en otra ubicación de almacenamiento distinta a la memoria del servidor. La persistencia del estado no es automática. Debe seguir los pasos al desarrollar la aplicación para implementar la persistencia de datos con estado.

La persistencia de los datos normalmente solo es necesaria para el estado de alto valor que los usuarios crearon. En los ejemplos siguientes, el estado persistente ahorra tiempo o ayuda en las actividades comerciales:

* Formularios web de varios pasos: en un formulario web que consta de varios pasos, en caso de que el estado se pierda, los usuarios tienen que dedicar mucho tiempo a volver a escribir los datos que ya han proporcionado. Un usuario pierde el estado en este escenario si se sale del formulario y vuelve más adelante.
* Carros de la compra: se puede mantener cualquier componente comercial importante de una aplicación que represente ingresos potenciales. Un usuario que pierde su estado y, por tanto, su carro de la compra, puede comprar menos productos o servicios cuando vuelva al sitio más adelante.

Una aplicación solo puede conservar el *estado de la aplicación*. Las interfaces de usuario no se pueden conservar, como instancias de componentes y sus árboles de representación. Los componentes y los árboles de representación no suelen ser serializables. Para conservar el estado de la interfaz de usuario, como los nodos expandidos de un control de vista de árbol, la aplicación debe tener código personalizado para modelar el comportamiento del estado de la interfaz de usuario como estado de la aplicación serializable.

## <a name="where-to-persist-state"></a>Dónde conservar el estado

Existen ubicaciones comunes para el estado persistente:

* [Almacenamiento del lado servidor](#server-side-storage-server)
* [URL](#url-server)
* [Almacenamiento del explorador](#browser-storage-server)
* [Servicio de contenedor de estado en memoria](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server">Almacenamiento del lado servidor</h2>

Para una persistencia de datos permanente que abarque varios usuarios y dispositivos, la aplicación puede usar el almacenamiento del lado servidor. Las opciones incluyen:

* Blob Storage
* Almacenamiento de pares clave-valor
* Base de datos relacional
* Almacenamiento de tablas

Una vez guardados los datos, el estado del usuario se conserva y está disponible en cualquier circuito nuevo.

Para obtener más información sobre las opciones de almacenamiento de datos de Azure, consulte lo siguiente:

* [Bases de datos de Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentación de Azure Storage](/azure/storage/)

<h2 id="url-server">Resolución</h2>

Para los datos transitorios que representan el estado de navegación, modele los datos como parte de la dirección URL. Entre los ejemplos de estado de usuario modelado en la dirección URL se incluyen:

* El identificador de una entidad visualizada.
* El número de la página actual en una cuadrícula paginada.

Se conserva el contenido de la barra de direcciones del explorador:

* Si el usuario recarga manualmente la página.
* Si el servidor web deja de estar disponible y el usuario se ve obligado a volver a cargar la página para conectarse a un servidor diferente.

Para obtener información sobre cómo definir patrones de direcciones URL con la directiva [`@page`](xref:mvc/views/razor#page), vea <xref:blazor/fundamentals/routing>.

<h2 id="browser-storage-server">Almacenamiento del explorador</h2>

En el caso de los datos transitorios que el usuario crea activamente, una ubicación de almacenamiento que se suele usar son las colecciones [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) del explorador:

* `localStorage` está en el ámbito de la ventana del explorador. Si el usuario recarga la página o la cierra y vuelve a abrir el explorador, el estado se conserva. Si el usuario abre varias pestañas del explorador, el estado se comparte entre las pestañas. Los datos se conservan en `localStorage` hasta que se borran explícitamente.
* `sessionStorage` está en el ámbito de la pestaña del explorador. Si el usuario recarga la pestaña, el estado se conserva. Si el usuario cierra la pestaña o el explorador, se pierde el estado. Si el usuario abre varias pestañas del explorador, cada pestaña tendrá su propia versión independiente de los datos.

Por lo general, es más seguro usar `sessionStorage`. `sessionStorage` evita el riesgo de que un usuario abra varias pestañas y encuentre lo siguiente:

* Errores en el almacenamiento de estado en todas las pestañas.
* Comportamiento confuso cuando una pestaña sobrescribe el estado de otras.

`localStorage` es la mejor opción si la aplicación debe conservar el estado en el cierre y volver a abrir el explorador.

Advertencias sobre el uso del almacenamiento del explorador:

* De forma similar al uso de una base de datos del lado servidor, la carga y el almacenamiento de datos son asincrónicos.
* A diferencia de las bases de datos del lado servidor, el almacenamiento no está disponible durante la representación previa porque la página solicitada no existe en el explorador durante la fase de representación previa.
* Es razonable almacenar unos pocos kilobytes de datos para las aplicaciones Blazor Server. Más allá de unos pocos kilobytes, debe tener en cuenta las implicaciones de rendimiento porque los datos se cargan y se guardan en la red.
* Los usuarios pueden ver o alterar los datos. La [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) puede mitigar el riesgo. Por ejemplo, el [almacenamiento del explorador protegido de ASP.NET Core](#aspnet-core-protected-browser-storage) usa la protección de datos de ASP.NET Core.

Los paquetes NuGet de terceros proporcionan API para trabajar con `localStorage` y `sessionStorage`. Merece la pena tener en cuenta la elección de un paquete que utiliza de forma transparente la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction). La protección de datos cifra los datos almacenados y reduce el riesgo potencial de alteración de los datos almacenados. Si los datos serializados con JSON se almacenan como texto sin formato, los usuarios pueden ver los datos mediante las herramientas de desarrollo del explorador y también modificar los datos almacenados. La protección de datos no siempre es un problema, ya que los datos pueden ser bastante triviales. Por ejemplo, la lectura o modificación del color almacenado de un elemento de la interfaz de usuario no supone un riesgo de seguridad significativo para el usuario o la organización. Evite que los usuarios puedan inspeccionar o manipular *datos confidenciales*.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>Almacenamiento del explorador protegido de ASP.NET Core

Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).

> [!NOTE]
> El almacenamiento del explorador protegido se basa en la protección de datos de ASP.NET Core y solo se admite para aplicaciones de Blazor Server.

### <a name="save-and-load-data-within-a-component"></a>Guardar y cargar datos dentro de un componente

En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

La elección depende de la ubicación del almacenamiento del explorador que desee usar. En el ejemplo siguiente, se usa `sessionStorage`:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

La directiva `@using` se puede colocar en el archivo `_Imports.razor` de la aplicación, en lugar de en el componente. El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.

Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable. Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo. `ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON para almacenar objetos de estado complejos.

En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario. Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core. Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.

Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito nuevo, use `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado no `null` en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página. Para obtener más información, vea <xref:blazor/components/lifecycle>.

> [!WARNING]
> Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa. Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.
>
> Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa. Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Controlar el estado de carga

Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente. Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.

Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando. En el componente `Counter` predeterminado, el recuento se mantiene en `int`. [Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):

```csharp
private int? currentCount;
```

En lugar de mostrar de manera incondicional el recuento y el botón **`Increment`** , muestre estos elementos solo si se cargan los datos comprobando <xref:System.Nullable%601.HasValue%2A>:

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

`localStorage` o `sessionStorage` no están disponibles durante la representación previa. Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.

Una manera de resolver el error es deshabilitar la representación previa. Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador. La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.

Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`. Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito. El siguiente es un ejemplo para almacenar un valor de contador:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factorizar la preservación de estado en una ubicación común

Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código. Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado. Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.

En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
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
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa. Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).

En general, se recomienda el patrón *componente primario del proveedor de estado*:

* Para consumir el estado en muchos componentes.
* Si solo hay un objeto de estado de nivel superior para conservar.

Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Paquete de NuGet experimental del almacenamiento de explorador protegido

Por ejemplo, el almacenamiento del explorador protegido de ASP.NET Core usa la [protección de datos de ASP.NET Core](xref:security/data-protection/introduction) para [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) y [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` es un paquete experimental no compatible que no es adecuado para su uso en producción.
>
> El paquete solo está disponible para su uso en aplicaciones Blazor Server de ASP.NET Core 3.1.

### <a name="configuration"></a>Configuración

1. Agregue una referencia de paquete a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. En el archivo `Pages/_Host.cshtml`, agregue el siguiente script dentro de la etiqueta de cierre `</body>`:

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. En `Startup.ConfigureServices`, llame a `AddProtectedBrowserStorage` para agregar los servicios `localStorage` y `sessionStorage` a la colección de servicios:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Guardar y cargar datos dentro de un componente

En cualquier componente que requiera cargar o guardar datos en el almacenamiento del explorador, use la directiva [`@inject`](xref:mvc/views/razor#inject) para insertar una instancia de uno de los elementos siguientes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

La elección depende de la ubicación del almacenamiento del explorador que desee usar. En el ejemplo siguiente, se usa `sessionStorage`:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

La instrucción `@using` se puede colocar en un archivo `_Imports.razor`, en lugar de en el componente. El uso del archivo `_Imports.razor` hace que el espacio de nombres esté disponible para segmentos más grandes de la aplicación o para toda la aplicación.

Para conservar el valor de `currentCount` en el componente `Counter` de una aplicación basada en la plantilla del proyecto Blazor Server, modifique el método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

En aplicaciones más grandes y realistas, el almacenamiento de campos individuales es un escenario improbable. Es más probable que las aplicaciones almacenen objetos de modelo completos que incluyen un estado complejo. `ProtectedSessionStore` serializa y deserializa automáticamente los datos de JSON.

En el ejemplo de código anterior, los datos de `currentCount` se almacenan como `sessionStorage['count']` en el explorador del usuario. Los datos no se almacenan como texto sin formato, sino que se protegen mediante la protección de datos de ASP.NET Core. Los datos cifrados pueden inspeccionarse si se evalúa `sessionStorage['count']` en la consola del desarrollador del explorador.

Para recuperar los datos de `currentCount` si el usuario vuelve al componente `Counter` más adelante, incluso si está en un circuito totalmente nuevo, use `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Si los parámetros del componente incluyen el estado de navegación, llame a `ProtectedSessionStore.GetAsync` y asigne el resultado en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> solo se llama una vez la primera vez que se crea una instancia del componente. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no se llama de nuevo más tarde si el usuario navega a una dirección URL diferente mientras permanece en la misma página. Para obtener más información, vea <xref:blazor/components/lifecycle>.

> [!WARNING]
> Los ejemplos de esta sección solo funcionan si el servidor no tiene habilitada la representación previa. Con la representación previa habilitada, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.
>
> Deshabilite la representación previa o agregue código adicional para trabajar con la representación previa. Para obtener más información sobre cómo escribir código que funcione con la representación previa, consulte la sección [Controlar la representación previa](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Controlar el estado de carga

Dado que se accede al almacenamiento del explorador de forma asincrónica a través de una conexión de red, siempre hay un período de tiempo antes de que los datos se carguen y estén disponibles para un componente. Para obtener los mejores resultados, represente un mensaje de estado de carga mientras la carga está en curso, en lugar de mostrar datos en blanco o predeterminados.

Un enfoque consiste en realizar un seguimiento de si los datos son `null`, lo que significa que los datos aún se están cargando. En el componente `Counter` predeterminado, el recuento se mantiene en `int`. [Haga que `currentCount` admita valores NULL](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) agregando un signo de interrogación (`?`) al tipo (`int`):

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

`localStorage` o `sessionStorage` no están disponibles durante la representación previa. Si el componente intenta interactuar con el almacenamiento, se genera un error que explica que no se pueden emitir llamadas de interoperabilidad de JavaScript porque se está realizando una representación previa del componente.

Una manera de resolver el error es deshabilitar la representación previa. Esta suele ser la mejor opción si la aplicación hace un uso intensivo del almacenamiento basado en explorador. La representación previa agrega complejidad y no beneficia a la aplicación porque esta no puede representar previamente ningún contenido útil hasta que `localStorage` o `sessionStorage` estén disponibles.

Para deshabilitar la representación previa, abra el archivo `Pages/_Host.cshtml` y cambie el atributo `render-mode` del [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) a <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

La representación previa puede resultar útil para otras páginas que no utilizan `localStorage` o `sessionStorage`. Para mantener la representación previa, postergue la operación de carga hasta que el explorador esté conectado al circuito. El siguiente es un ejemplo para almacenar un valor de contador:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factorizar la preservación de estado en una ubicación común

Si muchos componentes se basan en el almacenamiento basado en explorador, al volver a implementar el código del proveedor de estado muchas veces se crea la duplicación de código. Una opción para evitar la duplicación de código es crear un *componente primario del proveedor de estado* que encapsula la lógica del proveedor de estado. Los componentes secundarios pueden trabajar con datos persistentes sin tener en cuenta el mecanismo de persistencia del estado.

En el siguiente ejemplo de un componente `CounterStateProvider`, se conservan los datos del contador en `sessionStorage`:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
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
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

Para tratar la representación previa tal y como se ha descrito anteriormente, `CounterStateProvider` se puede modificar para que todos los componentes que consumen los datos del contador funcionen automáticamente con la representación previa. Para obtener más información, vea la sección [Controlar la representación previa](#handle-prerendering).

En general, se recomienda el patrón *componente primario del proveedor de estado*:

* Para consumir el estado en muchos componentes.
* Si solo hay un objeto de estado de nivel superior para conservar.

Para conservar muchos objetos de estado diferentes y consumir distintos subconjuntos de objetos en distintos lugares, es mejor evitar la conservación global del estado.

::: moniker-end

<h2 id="in-memory-state-container-service-server">Servicio de contenedor de estado en memoria</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
