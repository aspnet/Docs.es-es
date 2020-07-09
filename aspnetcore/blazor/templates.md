---
title: Plantillas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plantillas de aplicación de Blazor de ASP.NET Core y la estructura de proyecto de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: f1b131947a242323295a763ba2f2473af0ccfb4f
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944526"
---
# <a name="aspnet-core-blazor-templates"></a>Plantillas de Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

El marco Blazor proporciona plantillas que permiten desarrollar aplicaciones para cada uno de los modelos de hospedaje de Blazor:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.

Las opciones de plantilla están disponibles si se pasa la opción `--help` al comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Estructura de proyecto de Blazor

Los siguientes archivos y carpetas componen una aplicación Blazor generada a partir de una plantilla de Blazor:

* `Program.cs`: El punto de entrada de la aplicación que configura lo siguiente:

  * ASP.NET Core [hospedado](xref:fundamentals/host/generic-host) (Blazor Server)
  * Host de WebAssembly (Blazor WebAssembly): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de Blazor WebAssembly (`blazorwasm`).
    * El componente `App`, que es el componente raíz de la aplicación, se especifica como el elemento DOM `app` en el método `Add`.
    * Los servicios se pueden configurar con el método `ConfigureServices` en el generador del host (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * La configuración se puede proporcionar a través del generador del host (`builder.Configuration`).

* `Startup.cs` (Blazor Server): contiene la lógica de inicio de la aplicación. La clase `Startup` define dos métodos:

  * `ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación. En las aplicaciones Blazor Server, los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.
  * `Configure`: configura la canalización de administración de solicitudes de la aplicación:
    * Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> para configurar un punto de conexión para la conexión en tiempo real con el explorador. La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.
    * Se llama a [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (`Pages/_Host.cshtml`) y habilitar la navegación.

* `wwwroot/index.html` (Blazor WebAssembly): página raíz de la aplicación implementada como página HTML:
  * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
  * La página especifica dónde se va a representar el componente `App` raíz. El componente `App` (`App.razor`) se especifica como el elemento DOM `app` del método `AddComponent` en `Startup.Configure`.
  * Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:
    * Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.
    * Inicializa el runtime para ejecutar la aplicación.

* `App.razor`: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>. El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.

* Carpeta `Pages`: contiene las páginas o componentes enrutables (`.razor`) que conforman la aplicación Blazor y la página raíz de Razor de una aplicación Blazor Server. La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page). En la plantilla se incluye lo siguiente:
  * `_Host.cshtml` (Blazor Server): página raíz de la aplicación implementada como página Razor:
    * Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.
    * Se carga el archivo JavaScript `_framework/blazor.server.js`, que configura la conexión de SignalR en tiempo real entre el explorador y el servidor.
    * La página Host especifica dónde se va a representar el componente `App` raíz (`App.razor`).
  * `Counter` (`Pages/Counter.razor`): implementa la página Counter.
  * `Error` (`Error.razor`, solo aplicaciones Blazor Server): se representa cuando se produce una excepción no controlada en la aplicación.
  * `FetchData` (`Pages/FetchData.razor`): implementa la página Fetch data (Recuperar datos).
  * `Index` (`Pages/Index.razor`): implementa la página principal.

* Carpeta `Shared`: contiene otros componentes de interfaz de usuario (`.razor`) que la aplicación usa:
  * `MainLayout` (`MainLayout.razor`): componente de diseño de la aplicación.
  * `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral. Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor. El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.

* `_Imports.razor`: Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación (`.razor`), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.

* Carpeta `Data` (Blazor Server): contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService`, que proporcionan datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.

* `wwwroot`: carpeta [Web Root](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación.

* `appsettings.json` (Blazor Server): Opciones de configuración de la aplicación.
