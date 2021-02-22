---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Aprenda a administrar el enrutamiento de solicitudes en aplicaciones y a usar el componente NavLink en aplicaciones de Blazor para la navegación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279893"
---
# <a name="aspnet-core-blazor-routing"></a>Enrutamiento de Blazor de ASP.NET Core

En este artículo, aprenderá a administrar el enrutamiento de solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones de Blazor.

## <a name="route-templates"></a>Plantillas de ruta

El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> habilita el enrutamiento a componentes Razor en una aplicación de Blazor. El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> se usa en el componente `App` de aplicaciones de Blazor.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

Cuando se compila un componente Razor (`.razor`) con una [directiva `@page`](xref:mvc/views/razor#page), la clase de componente generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute> que especifica la plantilla de ruta del componente.

Cuando se inicia la aplicación, el ensamblado especificado como `AppAssembly` del enrutador se examina para recopilar información de ruta de los componentes de la aplicación que tienen un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>.

En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:

* Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros de ruta.
* Representa el componente especificado con su [diseño](xref:blazor/layouts), incluidos los diseños anidados adicionales.

Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para los componentes que no tengan especificado un diseño con la [directiva `@layout`](xref:blazor/layouts#specify-a-layout-in-a-component). Las plantillas de proyecto Blazor del marco de trabajo especifican el componente `MainLayout` (`Shared/MainLayout.razor`) como diseño predeterminado de la aplicación. Para más información sobre los diseños, vea <xref:blazor/layouts>.

Los componentes admiten varias plantillas de ruta mediante varias [directivas `@page`](xref:mvc/views/razor#page). El componente de ejemplo siguiente se carga en las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`.

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo `wwwroot/index.html` (Blazor WebAssembly) o en su archivo `Pages/_Host.cshtml` (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href`. Para obtener más información, vea <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Suministro de contenido personalizado cuando no se encuentra contenido

El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.

En el componente `App`, establezca el contenido personalizado en la plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

Los elementos arbitrarios se admiten como contenido de las etiquetas `<NotFound>`, como otros componentes interactivos. Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts#default-layout>.

## <a name="route-to-components-from-multiple-assemblies"></a>Ruta a componentes desde varios ensamblados

Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables. Se examinan los ensamblados adicionales, además del ensamblado especificado para `AppAssembly`. En el siguiente ejemplo, `Component1` es un componente enrutable definido en una [biblioteca de clases de componentes](xref:blazor/components/class-libraries) a la que se hace referencia. El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a>Parámetros de ruta

El enrutador usa parámetros de ruta para rellenar los [parámetros de componente](xref:blazor/components/index#component-parameters) correspondientes con el mismo nombre. Los nombres de parámetros de ruta no distinguen mayúsculas de minúsculas. En el ejemplo siguiente, el parámetro `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente. Cuando se realiza una solicitud de `/RouteParameter/amazing`, el contenido de la etiqueta `<h1>` se representa como `Blazor is amazing!`.

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Se admiten parámetros opcionales. En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente. Si el segmento no está presente, el valor de `Text` se establece en `fantastic`.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

No se admiten parámetros opcionales. En el ejemplo siguiente, se aplican dos [directivas `@page`](xref:mvc/views/razor#page). La primera directiva permite navegar al componente sin un parámetro, mientras que la segunda directiva asigna el valor del parámetro de ruta `{text}` a la propiedad `Text` del componente.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set), en lugar de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods), para permitir la navegación de la aplicación al mismo componente con un valor de parámetro opcional diferente. Según el ejemplo anterior, use `OnParametersSet` cuando el usuario pueda navegar desde `/RouteParameter` a `/RouteParameter/amazing`, o desde `/RouteParameter/amazing` a `/RouteParameter`:

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Restricciones de ruta

Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.

En el siguiente ejemplo, la ruta al componente `User` solo coincide en estos casos:

* Existe un segmento de ruta `Id` en la dirección URL de la solicitud.
* El segmento `Id` es un tipo de entero (`int`).

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

En la siguiente tabla figuran las restricciones de ruta que hay disponibles. Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, consulte la advertencia que aparece después de la tabla.

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

## <a name="routing-with-urls-that-contain-dots"></a>Enrutamiento con direcciones URL que contienen puntos

En el caso de las aplicaciones hospedadas de Blazor WebAssembly y Blazor Server, la plantilla de ruta predeterminada del lado servidor presupone que, si el último segmento de una dirección URL de solicitud contiene un punto (`.`), se solicita un archivo. Por ejemplo, el enrutador interpreta la dirección URL `https://localhost.com:5001/example/some.thing` como una solicitud de un archivo denominado `some.thing`. Sin configuración adicional, una aplicación devuelve una respuesta *404: no encontrado* si se pretendía enrutar `some.thing` a un componente con una [directiva `@page`](xref:mvc/views/razor#page) y `some.thing` es un valor de parámetro de ruta. Para usar una ruta con uno o más parámetros que contengan un punto, la aplicación debe configurar la ruta con una plantilla personalizada.

Tenga en cuenta el siguiente componente `Example` que puede recibir un parámetro de ruta del último segmento de la dirección URL.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

Para permitir que la aplicación *`Server`* de una solución de Blazor WebAssembly hospedada enrute la solicitud con un punto en el parámetro `param`, agregue una plantilla de ruta de archivo de reserva con el parámetro opcional en `Startup.Configure`.

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Para configurar una aplicación Blazor Server para enrutar la solicitud con un punto en el parámetro de ruta `param`, agregue una plantilla de ruta de página de reserva con el parámetro opcional en `Startup.Configure`.

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Para obtener más información, vea <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Parámetros de ruta de captura general

::: moniker range=">= aspnetcore-5.0"

Los parámetros de ruta de captura general, que capturan rutas de acceso en varios límites de carpeta, se admiten en los componentes.

Los parámetros de ruta de captura general son:

* Con nombre para que coincida con el nombre del segmento de ruta. La nomenclatura no distingue mayúsculas de minúsculas.
* Tipo `string`. El marco no proporciona conversión automática.
* Al final de la dirección URL.

`Pages/CatchAll.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

Para la dirección URL `/catch-all/this/is/a/test` con una plantilla de ruta de `/catch-all/{*pageRoute}`, el valor de `PageRoute` se establece en `this/is/a/test`.

Las barras diagonales y los segmentos de la ruta de acceso capturada están descodificados. En el caso de una plantilla de ruta de `/catch-all/{*pageRoute}`, la dirección URL `/catch-all/this/is/a%2Ftest%2A` produce `this/is/a/test*`.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Los parámetros de ruta de captura general se admiten en ASP.NET Core 5.0 o posterior. Para más información, seleccione la versión 5.0 de este artículo.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>Aplicaciones auxiliares de URI y estado de navegación

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para administrar los URI y la navegación en el código de C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.

| Miembro | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtiene el URI absoluto actual. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto. Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en `wwwroot/index.html` (Blazor WebAssembly) o `Pages/_Host.cshtml` (Blazor Server). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega al URI especificado. Si `forceLoad` es `true`:<ul><li>El enrutamiento del lado cliente se omite.</li><li>Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Evento que se desencadena cuando la ubicación de navegación ha cambiado. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convierte un URI relativo en un URI absoluto. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base. |

En el caso del evento <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged>, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre los eventos de navegación:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador. Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.

El siguiente componente:

* Navega al componente `Counter` de la aplicación (`Pages/Counter.razor`) cuando se selecciona el botón con <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.
* Controla el evento de ubicación cambiado mediante la suscripción a <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.
  * El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`. Al desenlazar el método se permite la recolección de elementos no utilizados del componente.
  * La implementación del registrador registra la siguiente información cuando se selecciona el botón:

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="query-string-and-parse-parameters"></a>Parámetros de cadena de consulta y de análisis

La cadena de consulta de una solicitud se obtiene de la propiedad <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>:

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

Para analizar los parámetros de una cadena de consulta:

* Una aplicación puede usar la API <xref:Microsoft.AspNetCore.WebUtilities>. Si la API no está disponible para la aplicación, agregue una referencia de paquete en el archivo de proyecto de la aplicación para [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Obtenga el valor después de analizar la cadena de consulta con <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.

En el siguiente ejemplo del componente `ParseQueryString` se analiza una clave de parámetro de cadena de consulta denominada `ship`. Por ejemplo, el par clave-valor de la cadena de consulta de dirección URL `?ship=Tardis` captura el valor `Tardis` en `queryValue`. En el ejemplo siguiente, vaya a la aplicación con la dirección URL `https://localhost:5001/parse-query-string?ship=Tardis`.

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a>componentes `NavLink` y `NavMenu`

Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación. Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual. La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados. Opcionalmente, asigne un nombre de clase CSS a <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar una clase CSS personalizada al vínculo representado cuando la ruta actual coincida con `href`.

El siguiente componente `NavMenu` crea una barra de navegación de [`Bootstrap`](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> El componente `NavMenu` (`NavMenu.razor`) se proporciona en la carpeta `Shared` de una aplicación generada a partir de las plantillas de proyecto de Blazor.

Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.

En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`). El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `component` (por ejemplo, `https://localhost:5001/component` y `https://localhost:5001/component/another-segment`).

Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada. En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Se representa el siguiente marcado HTML:

```html
<a href="example-page" target="_blank">Example page</a>
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

## <a name="aspnet-core-endpoint-routing-integration"></a>Integración del enrutamiento de puntos de conexión de ASP.NET Core

*Esta sección solo se aplica a las aplicaciones Blazor Server.*

Blazor Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing). Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`.

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

La configuración típica consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server. Convencionalmente, la página del *host* se suele llamar `_Host.cshtml`en la carpeta `Pages` de la aplicación.

La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas, y se solo se usa cuando no se encuentran coincidencias con otras rutas. Esto permite a la aplicación usar otros controladores y páginas sin interferir con el enrutamiento de componentes en la aplicación Blazor Server.

Para información sobre cómo configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> para el hospedaje de servidores de direcciones URL no raíz, consulte <xref:blazor/host-and-deploy/index#app-base-path>.
