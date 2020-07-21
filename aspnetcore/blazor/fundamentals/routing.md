---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo enrutar solicitudes en aplicaciones y sobre el componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 4f85c4a9803482f39446dda599f10829c9879f27
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407767"
---
# <a name="aspnet-core-blazor-routing"></a>Enrutamiento de Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Aprenda a enrutar solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integración del enrutamiento de puntos de conexión de ASP.NET Core

Blazor Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing). Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server. Convencionalmente, la página del *host* se suele llamar `_Host.cshtml`. La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas, y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas. Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación Blazor Server.

Para información sobre cómo configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> para el hospedaje de servidores de direcciones URL no raíz, consulte <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="route-templates"></a>Plantillas de ruta

El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite el enrutamiento a cada componente con una ruta especificada. El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> aparece en el archivo `App.razor`:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Cuando se compila un archivo `.razor` con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.

En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:

* Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros deseados.
* Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.

Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño. Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`. `MainLayout.razor` está en la carpeta `Shared` del proyecto de plantilla. Para más información sobre los diseños, vea <xref:blazor/layouts>.

Se pueden aplicar varias plantillas de ruta a un componente. El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo `wwwroot/index.html` (Blazor WebAssembly) o en su archivo `Pages/_Host.cshtml` (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`). Para obtener más información, vea <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Suministro de contenido personalizado cuando no se encuentra contenido

El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.

En el archivo `App.razor`, establezca el contenido personalizado en el parámetro de plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos. Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Ruta a componentes desde varios ensamblados

Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables. Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`. En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia. El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parámetros de ruta

El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

No se admiten parámetros opcionales. En el ejemplo anterior se aplican dos directivas `@page`. La primera permite navegar al componente sin un parámetro, mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.

## <a name="route-constraints"></a>Restricciones de ruta

Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.

En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:

* Existe un segmento de ruta `Id` en la dirección URL de la solicitud.
* El segmento `Id` es un entero (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

En la siguiente tabla figuran las restricciones de ruta que hay disponibles. Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.

| Restricción | Ejemplo           | Coincidencias de ejemplo                                                                  | Invariable<br>referencia cultural<br>coincidencia |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sí                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sí                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sí                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sí                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sí                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sí                              |

> [!WARNING]
> Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o <xref:System.DateTime>) usan siempre la referencia cultural invariable. Estas restricciones dan por supuesto que la dirección URL no es localizable.

### <a name="routing-with-urls-that-contain-dots"></a>Enrutamiento con direcciones URL que contienen puntos

En las aplicaciones Blazor Server, la ruta predeterminada en `_Host.cshtml` es `/` (`@page "/"`). Una dirección URL de solicitud que contiene un punto (`.`) no coincide con la ruta predeterminada porque la dirección URL parece que solicita un archivo. Una aplicación Blazor devuelve una respuesta *404 No encontrado* cuando un archivo estático no existe. Para usar rutas que contienen un punto, configure `_Host.cshtml` con la siguiente plantilla de ruta:

```cshtml
@page "/{**path}"
```

La plantilla `"/{**path}"` incluye lo siguiente:

* Una sintaxis de *captura general* de doble asterisco (`**`) para capturar la ruta de acceso en varios límites de carpeta sin codificar las barras diagonales (`/`)
* El nombre del parámetro de ruta `path`

> [!NOTE]
> La sintaxis de parámetro *Catch-all* (`*`/`**`) **no** se admite en componentes Razor (`.razor`).

Para obtener más información, vea <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación. Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual. La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados. Opcionalmente, asigne un nombre de clase CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar una clase CSS personalizada al vínculo representado cuando la ruta actual coincida con `href`.

El siguiente componente `NavMenu` crea una barra de navegación de [`Bootstrap`](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.

En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`). El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).

Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada. En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Se representa el siguiente marcado HTML:

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes `NavLink` dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente `NavLink` (secundario):
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> El uso de una variable de índice en este escenario es un requisito para **cualquier** componente secundario que use una variable de bucle en su [contenido secundario](xref:blazor/components/index#child-content), no solo para el componente `NavLink`.
>
> También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>Aplicaciones auxiliares de URI y estado de navegación

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.

| Miembro | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtiene el URI absoluto actual. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto. Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega al URI especificado. Si `forceLoad` es `true`:<ul><li>El enrutamiento del lado cliente se omite.</li><li>Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Evento que se desencadena cuando la ubicación de navegación ha cambiado. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convierte un URI relativo en un URI absoluto. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base. |

El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

El componente siguiente controla un evento de cambio de ubicación suscribiéndose a <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>. El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`. Al desenlazar el método se permite la recolección de elementos no utilizados del componente.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador. Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.

Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="query-string-and-parse-parameters"></a>Parámetros de cadena de consulta y de análisis

La cadena de consulta de una solicitud se puede obtener de la propiedad <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> de <xref:Microsoft.AspNetCore.Components.NavigationManager>:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Para analizar los parámetros de una cadena de consulta:

* Agregue una referencia de paquete para [Microsoft.AspNetCore.webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Obtenga el valor después de analizar la cadena de consulta con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

El marcador de posición `{KEY}` del ejemplo anterior es la clave del parámetro de la cadena de consulta. Por ejemplo, el par clave-valor de dirección URL `?ship=Tardis` utiliza una clave de `ship`.
