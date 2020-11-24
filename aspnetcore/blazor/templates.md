---
title: Plantillas de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre las plantillas de aplicación de Blazor de ASP.NET Core y la estructura de proyecto de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703727"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="2176c-103">Plantillas de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2176c-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="2176c-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2176c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2176c-105">El marco Blazor proporciona plantillas que permiten desarrollar aplicaciones para cada uno de los modelos de hospedaje de Blazor:</span><span class="sxs-lookup"><span data-stu-id="2176c-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="2176c-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="2176c-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="2176c-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="2176c-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="2176c-108">Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2176c-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="2176c-109">Las opciones de plantilla están disponibles si se pasa la opción `--help` al comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la CLI:</span><span class="sxs-lookup"><span data-stu-id="2176c-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="2176c-110">Estructura de proyecto de Blazor</span><span class="sxs-lookup"><span data-stu-id="2176c-110">Blazor project structure</span></span>

<span data-ttu-id="2176c-111">Los siguientes archivos y carpetas componen una aplicación Blazor generada a partir de una plantilla de proyecto Blazor:</span><span class="sxs-lookup"><span data-stu-id="2176c-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="2176c-112">`Program.cs`: El punto de entrada de la aplicación que configura lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2176c-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="2176c-113">ASP.NET Core [hospedado](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="2176c-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="2176c-114">Host de WebAssembly (Blazor WebAssembly): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="2176c-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="2176c-115">El componente `App` es el componente raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="2176c-116">El componente `App` se especifica como el elemento DOM `app` (`<div id="app">Loading...</div>` en `wwwroot/index.html`) de la colección de componentes raíz (`builder.RootComponents.Add<App>("#app")`).</span><span class="sxs-lookup"><span data-stu-id="2176c-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="2176c-117">Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="2176c-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="2176c-118">`Program.cs`: El punto de entrada de la aplicación que configura lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2176c-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="2176c-119">ASP.NET Core [hospedado](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="2176c-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="2176c-120">Host de WebAssembly (Blazor WebAssembly): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="2176c-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="2176c-121">El componente `App` es el componente raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="2176c-122">El componente `App` se especifica como el elemento DOM `app` (`<app>Loading...</app>` en `wwwroot/index.html`) de la colección de componentes raíz (`builder.RootComponents.Add<App>("app")`).</span><span class="sxs-lookup"><span data-stu-id="2176c-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="2176c-123">Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="2176c-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="2176c-124">`Startup.cs` (Blazor Server): contiene la lógica de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="2176c-125">La clase `Startup` define dos métodos:</span><span class="sxs-lookup"><span data-stu-id="2176c-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="2176c-126">`ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="2176c-127">En las aplicaciones Blazor Server, los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="2176c-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="2176c-128">`Configure`: configura la canalización de administración de solicitudes de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="2176c-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="2176c-129">Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> para configurar un punto de conexión para la conexión en tiempo real con el explorador.</span><span class="sxs-lookup"><span data-stu-id="2176c-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="2176c-130">La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="2176c-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="2176c-131">Se llama a [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (`Pages/_Host.cshtml`) y habilitar la navegación.</span><span class="sxs-lookup"><span data-stu-id="2176c-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="2176c-132">`wwwroot/index.html` (Blazor WebAssembly): página raíz de la aplicación implementada como página HTML:</span><span class="sxs-lookup"><span data-stu-id="2176c-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="2176c-133">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="2176c-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="2176c-134">La página especifica dónde se va a representar el componente `App` raíz.</span><span class="sxs-lookup"><span data-stu-id="2176c-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="2176c-135">El componente se representa en la ubicación del elemento DOM `app` (`<app>...</app>`).</span><span class="sxs-lookup"><span data-stu-id="2176c-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="2176c-136">Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2176c-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="2176c-137">Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="2176c-138">Inicializa el runtime para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="2176c-139">`App.razor`: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="2176c-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="2176c-140">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.</span><span class="sxs-lookup"><span data-stu-id="2176c-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="2176c-141">Carpeta `Pages`: contiene las páginas o componentes enrutables (`.razor`) que conforman la aplicación Blazor y la página raíz de Razor de una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2176c-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="2176c-142">La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="2176c-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="2176c-143">En la plantilla se incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2176c-143">The template includes the following:</span></span>
  * <span data-ttu-id="2176c-144">`_Host.cshtml` (Blazor Server): página raíz de la aplicación implementada como página Razor:</span><span class="sxs-lookup"><span data-stu-id="2176c-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="2176c-145">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="2176c-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="2176c-146">Se carga el archivo JavaScript `_framework/blazor.server.js`, que configura la conexión de SignalR en tiempo real entre el explorador y el servidor.</span><span class="sxs-lookup"><span data-stu-id="2176c-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="2176c-147">La página Host especifica dónde se va a representar el componente `App` raíz (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="2176c-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="2176c-148">Componente `Counter` (`Pages/Counter.razor`): implementa la página Counter.</span><span class="sxs-lookup"><span data-stu-id="2176c-148">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="2176c-149">Componente `Error` (`Error.razor`, solo en la aplicación Blazor Server): se representa cuando se produce una excepción no controlada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-149">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="2176c-150">Componente `FetchData` (`Pages/FetchData.razor`): implementa la página Fetch data (Recuperar datos).</span><span class="sxs-lookup"><span data-stu-id="2176c-150">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="2176c-151">Componente `Index` (`Pages/Index.razor`): implementa la página principal.</span><span class="sxs-lookup"><span data-stu-id="2176c-151">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="2176c-152">`Properties/launchSettings.json`: Contiene la [configuración del entorno de desarrollo](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="2176c-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="2176c-153">Carpeta `Shared`: contiene otros componentes de interfaz de usuario (`.razor`) que la aplicación usa:</span><span class="sxs-lookup"><span data-stu-id="2176c-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="2176c-154">Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-154">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="2176c-155">`MainLayout.razor.css`: hoja de estilos del diseño principal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-155">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="2176c-156">Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="2176c-156">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="2176c-157">Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="2176c-157">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="2176c-158">El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.</span><span class="sxs-lookup"><span data-stu-id="2176c-158">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="2176c-159">`NavMenu.razor.css`: hoja de estilos del menú de navegación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-159">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="2176c-160">Carpeta `Shared`: contiene otros componentes de interfaz de usuario (`.razor`) que la aplicación usa:</span><span class="sxs-lookup"><span data-stu-id="2176c-160">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="2176c-161">Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-161">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="2176c-162">Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="2176c-162">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="2176c-163">Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="2176c-163">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="2176c-164">El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.</span><span class="sxs-lookup"><span data-stu-id="2176c-164">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="2176c-165">`_Imports.razor`: Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación (`.razor`), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="2176c-165">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="2176c-166">Carpeta `Data` (Blazor Server): contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService`, que proporcionan datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-166">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="2176c-167">`wwwroot`: carpeta [Web Root](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-167">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="2176c-168">`appsettings.json`: Contiene las [opciones de configuración](xref:blazor/fundamentals/configuration) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2176c-168">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="2176c-169">En una aplicación Blazor WebAssembly, el archivo de configuración de la aplicación se encuentra en la carpeta `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2176c-169">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="2176c-170">En una aplicación Blazor Server, el archivo de configuración de la aplicación se encuentra en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2176c-170">In a Blazor Server app, the app settings file is located at the project root.</span></span>
