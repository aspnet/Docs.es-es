---
title: Estructura del proyecto de Blazor de ASP.NET Core
author: guardrex
description: Conozca la estructura del proyecto de la aplicación de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751547"
---
# <a name="aspnet-core-blazor-project-structure"></a>Estructura del proyecto de Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

En este artículo se describen los archivos y las carpetas que componen una aplicación Blazor generada a partir de las plantillas de proyecto Blazor.

## Blazor WebAssembly

La plantilla Blazor WebAssembly (`blazorwasm`) crea los archivos iniciales y la estructura de directorios para una aplicación Blazor WebAssembly. La aplicación se rellena con código de demostración para un componente `FetchData` que carga datos de un recurso estático, `weather.json`, y la interacción del usuario con un componente `Counter`.

* Carpeta `Pages`: contiene los componentes o páginas enrutables (`.razor`) que componen la aplicación Blazor. La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page). La plantilla incluye los siguientes componentes:
  * Componente `Counter` (`Counter.razor`): implementa la página Counter.
  * Componente `FetchData` (`FetchData.razor`): implementa la página Fetch data (Recuperar datos).
  * Componente `Index` (`Index.razor`): implementa la página principal.
  
* `Properties/launchSettings.json`: Contiene la [configuración del entorno de desarrollo](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* Carpeta `Shared`: contiene los siguientes componentes y hojas de estilos compartidos:
  * Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.
  * `MainLayout.razor.css`: hoja de estilos del diseño principal de la aplicación.
  * Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral. Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor. El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.
  * `NavMenu.razor.css`: hoja de estilos del menú de navegación de la aplicación.
  * Componente `SurveyPrompt` (`SurveyPrompt.razor`): componente de encuesta Blazor.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Carpeta `Shared`: contiene los siguientes componentes compartidos:
  * Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.
  * Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral. Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor. El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.
  * Componente `SurveyPrompt` (`SurveyPrompt.razor`): componente de encuesta Blazor.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`; La carpeta [raíz web](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación, incluidos `appsettings.json` y los archivos de configuración de la aplicación de entorno para los [valores de configuración](xref:blazor/fundamentals/configuration). La página web `index.html` es la página raíz de la aplicación implementada como página HTML:
  * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
  * La página especifica dónde se va a representar el componente `App` raíz. El componente se representa en la ubicación del elemento DOM `div` con un elemento `id` de `app` (`<div id="app">Loading...</div>`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`; La carpeta [raíz web](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación, incluidos `appsettings.json` y los archivos de configuración de la aplicación de entorno para los [valores de configuración](xref:blazor/fundamentals/configuration). La página web `index.html` es la página raíz de la aplicación implementada como página HTML:
  * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
  * La página especifica dónde se va a representar el componente `App` raíz. El componente se representa en la ubicación del elemento DOM `app` (`<app>Loading...</app>`).

::: moniker-end

> [!NOTE]
> Los archivos de JavaScript (JS) agregados al archivo `wwwroot/index.html` deben aparecer antes de la etiqueta `</body>` de cierre. El orden en que se carga el código personalizado de JS desde los archivos JS es importante en algunos escenarios. Por ejemplo, asegúrese de que los archivos JS con métodos de interoperabilidad se incluyen antes que los archivos JS del marco Blazor.

* `_Imports.razor`: Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación (`.razor`), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.

* `App.razor`: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>. El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`; El punto de entrada de la aplicación que configura el host de WebAssembly:
  
  * El componente `App` es el componente raíz de la aplicación. El componente `App` se especifica como el elemento DOM `div` con un elemento `id` de `app` (`<div id="app">Loading...</div>` en `wwwroot/index.html`) en la colección de componentes raíz (`builder.RootComponents.Add<App>("#app")`).
  * Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`; El punto de entrada de la aplicación que configura el host de WebAssembly:

  * El componente `App` es el componente raíz de la aplicación. El componente `App` se especifica como el elemento DOM `app` (`<app>Loading...</app>` en `wwwroot/index.html`) de la colección de componentes raíz (`builder.RootComponents.Add<App>("app")`).
  * Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

## Blazor Server

La plantilla Blazor Server (`blazorserver`) crea los archivos iniciales y la estructura de directorios para una aplicación Blazor Server. La aplicación se rellena con código de demostración para un componente `FetchData` que carga datos de un servicio registrado, `WeatherForecastService`, y la interacción del usuario con un componente `Counter`.

* Carpeta `Data`: contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService` que proporciona datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.

* Carpeta `Pages`: contiene las páginas o componentes enrutables (`.razor`) que conforman la aplicación Blazor y la página raíz de Razor de una aplicación Blazor Server. La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page). En la plantilla se incluye lo siguiente:
  * `_Host.cshtml`; página raíz de la aplicación implementada como página Razor:
    * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
    * La página Host especifica dónde se va a representar el componente `App` raíz (`App.razor`).
  * Componente `Counter` (`Counter.razor`): implementa la página Counter.
  * Componente `Error` (`Error.razor`): se representa cuando se produce una excepción no controlada en la aplicación.
  * Componente `FetchData` (`FetchData.razor`): implementa la página Fetch data (Recuperar datos).
  * Componente `Index` (`Index.razor`): implementa la página principal.

> [!NOTE]
> Los archivos de JavaScript (JS) agregados al archivo `Pages/_Host.cshtml` deben aparecer antes de la etiqueta `</body>` de cierre. El orden en que se carga el código personalizado de JS desde los archivos JS es importante en algunos escenarios. Por ejemplo, asegúrese de que los archivos JS con métodos de interoperabilidad se incluyen antes que los archivos JS del marco Blazor.

* `Properties/launchSettings.json`: Contiene la [configuración del entorno de desarrollo](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* Carpeta `Shared`: contiene los siguientes componentes y hojas de estilos compartidos:
  * Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.
  * `MainLayout.razor.css`: hoja de estilos del diseño principal de la aplicación.
  * Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral. Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor. El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.
  * `NavMenu.razor.css`: hoja de estilos del menú de navegación de la aplicación.
  * Componente `SurveyPrompt` (`SurveyPrompt.razor`): componente de encuesta Blazor.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Carpeta `Shared`: contiene los siguientes componentes compartidos:
  * Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.
  * Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral. Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor. El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.
  * Componente `SurveyPrompt` (`SurveyPrompt.razor`): componente de encuesta Blazor.
  
::: moniker-end

* `wwwroot`: carpeta [Web Root](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación.

* `_Imports.razor`: Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación (`.razor`), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.

* `App.razor`: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>. El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.

* `appsettings.json` y archivos de configuración de la aplicación de entorno: Proporciona los [valores de configuración](xref:blazor/fundamentals/configuration) de la aplicación.

* `Program.cs`; El punto de entrada de la aplicación que configura el [host](xref:fundamentals/host/generic-host) de ASP.NET Core.

* `Startup.cs`; contiene la lógica de inicio de la aplicación. La clase `Startup` define dos métodos:

  * `ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación. Los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.
  * `Configure`: configura la canalización de administración de solicitudes de la aplicación:
    * Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> para configurar un punto de conexión para la conexión en tiempo real con el explorador. La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.
    * Se llama a [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (`Pages/_Host.cshtml`) y habilitar la navegación.
