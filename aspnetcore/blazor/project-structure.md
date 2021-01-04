---
title: Estructura del proyecto de Blazor de ASP.NET Core
author: guardrex
description: Conozca la estructura del proyecto de la aplicación de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513538"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="64ea7-103">Estructura del proyecto de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64ea7-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="64ea7-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64ea7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="64ea7-105">Los siguientes archivos y carpetas componen una aplicación Blazor generada a partir de una plantilla de proyecto Blazor:</span><span class="sxs-lookup"><span data-stu-id="64ea7-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="64ea7-106">`Program.cs`: El punto de entrada de la aplicación que configura lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ea7-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="64ea7-107">ASP.NET Core [hospedado](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="64ea7-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="64ea7-108">Host de WebAssembly (Blazor WebAssembly): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="64ea7-109">El componente `App` es el componente raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="64ea7-110">El componente `App` se especifica como el elemento DOM `div` con un elemento `id` de `app` (`<div id="app">Loading...</div>` en `wwwroot/index.html`) en la colección de componentes raíz (`builder.RootComponents.Add<App>("#app")`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="64ea7-111">Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="64ea7-112">`Program.cs`: El punto de entrada de la aplicación que configura lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ea7-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="64ea7-113">ASP.NET Core [hospedado](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="64ea7-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="64ea7-114">Host de WebAssembly (Blazor WebAssembly): el código de este archivo es único en las aplicaciones creadas a partir de la plantilla de Blazor WebAssembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="64ea7-115">El componente `App` es el componente raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="64ea7-116">El componente `App` se especifica como el elemento DOM `app` (`<app>Loading...</app>` en `wwwroot/index.html`) de la colección de componentes raíz (`builder.RootComponents.Add<App>("app")`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="64ea7-117">Los [servicios](xref:blazor/fundamentals/dependency-injection) se agregan y configuran (por ejemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="64ea7-118">`Startup.cs` (Blazor Server): contiene la lógica de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="64ea7-119">La clase `Startup` define dos métodos:</span><span class="sxs-lookup"><span data-stu-id="64ea7-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="64ea7-120">`ConfigureServices`: configura los servicios de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="64ea7-121">En las aplicaciones Blazor Server, los servicios se agregan llamando a <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, y el elemento `WeatherForecastService` se agrega al contenedor de servicios para que lo use el componente `FetchData` de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="64ea7-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="64ea7-122">`Configure`: configura la canalización de administración de solicitudes de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="64ea7-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="64ea7-123">Se llama a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> para configurar un punto de conexión para la conexión en tiempo real con el explorador.</span><span class="sxs-lookup"><span data-stu-id="64ea7-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="64ea7-124">La conexión se crea con [SignalR](xref:signalr/introduction), que es un marco para agregar funcionalidad web a las aplicaciones en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="64ea7-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="64ea7-125">Se llama a [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) para configurar la página raíz de la aplicación (`Pages/_Host.cshtml`) y habilitar la navegación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="64ea7-126">`wwwroot/index.html` (Blazor WebAssembly): página raíz de la aplicación implementada como página HTML:</span><span class="sxs-lookup"><span data-stu-id="64ea7-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="64ea7-127">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="64ea7-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="64ea7-128">La página especifica dónde se va a representar el componente `App` raíz.</span><span class="sxs-lookup"><span data-stu-id="64ea7-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="64ea7-129">El componente se representa en la ubicación del elemento DOM `div` con un elemento `id` de `app` (`<div id="app">Loading...</div>`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="64ea7-130">Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ea7-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="64ea7-131">Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="64ea7-132">Inicializa el runtime para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="64ea7-133">`wwwroot/index.html` (Blazor WebAssembly): página raíz de la aplicación implementada como página HTML:</span><span class="sxs-lookup"><span data-stu-id="64ea7-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="64ea7-134">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="64ea7-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="64ea7-135">La página especifica dónde se va a representar el componente `App` raíz.</span><span class="sxs-lookup"><span data-stu-id="64ea7-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="64ea7-136">El componente se representa en la ubicación del elemento DOM `app` (`<app>Loading...</app>`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="64ea7-137">Se carga el archivo JavaScript `_framework/blazor.webassembly.js`, que hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ea7-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="64ea7-138">Descarga el runtime de .NET, la aplicación y las dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="64ea7-139">Inicializa el runtime para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="64ea7-140">`App.razor`: componente raíz de la aplicación que configura el enrutamiento del lado cliente mediante el componente <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="64ea7-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="64ea7-141">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepta la navegación del explorador y representa la página que coincide con la dirección solicitada.</span><span class="sxs-lookup"><span data-stu-id="64ea7-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="64ea7-142">Carpeta `Pages`: contiene las páginas o componentes enrutables (`.razor`) que conforman la aplicación Blazor y la página raíz de Razor de una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="64ea7-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="64ea7-143">La ruta de cada página se especifica usando la directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="64ea7-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="64ea7-144">En la plantilla se incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ea7-144">The template includes the following:</span></span>
  * <span data-ttu-id="64ea7-145">`_Host.cshtml` (Blazor Server): página raíz de la aplicación implementada como página Razor:</span><span class="sxs-lookup"><span data-stu-id="64ea7-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="64ea7-146">Cuando se solicita inicialmente cualquier página de la aplicación, esta página se representa y se devuelve en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="64ea7-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="64ea7-147">Se carga el archivo JavaScript `_framework/blazor.server.js`, que configura la conexión de SignalR en tiempo real entre el explorador y el servidor.</span><span class="sxs-lookup"><span data-stu-id="64ea7-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="64ea7-148">La página Host especifica dónde se va a representar el componente `App` raíz (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="64ea7-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="64ea7-149">Componente `Counter` (`Pages/Counter.razor`): implementa la página Counter.</span><span class="sxs-lookup"><span data-stu-id="64ea7-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="64ea7-150">Componente `Error` (`Error.razor`, solo en la aplicación Blazor Server): se representa cuando se produce una excepción no controlada en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="64ea7-151">Componente `FetchData` (`Pages/FetchData.razor`): implementa la página Fetch data (Recuperar datos).</span><span class="sxs-lookup"><span data-stu-id="64ea7-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="64ea7-152">Componente `Index` (`Pages/Index.razor`): implementa la página principal.</span><span class="sxs-lookup"><span data-stu-id="64ea7-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="64ea7-153">`Properties/launchSettings.json`: Contiene la [configuración del entorno de desarrollo](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="64ea7-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="64ea7-154">Carpeta `Shared`: contiene otros componentes de interfaz de usuario (`.razor`) que la aplicación usa:</span><span class="sxs-lookup"><span data-stu-id="64ea7-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="64ea7-155">Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="64ea7-156">`MainLayout.razor.css`: hoja de estilos del diseño principal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="64ea7-157">Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="64ea7-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="64ea7-158">Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="64ea7-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="64ea7-159">El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.</span><span class="sxs-lookup"><span data-stu-id="64ea7-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="64ea7-160">`NavMenu.razor.css`: hoja de estilos del menú de navegación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="64ea7-161">Carpeta `Shared`: contiene otros componentes de interfaz de usuario (`.razor`) que la aplicación usa:</span><span class="sxs-lookup"><span data-stu-id="64ea7-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="64ea7-162">Componente `MainLayout` (`MainLayout.razor`): El [componente de diseño](xref:blazor/layouts) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="64ea7-163">Componente `NavMenu` (`NavMenu.razor`): implementa la navegación de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="64ea7-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="64ea7-164">Incluye el [componente `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que representa los vínculos de navegación a otros componentes Razor.</span><span class="sxs-lookup"><span data-stu-id="64ea7-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="64ea7-165">El componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indica automáticamente un estado seleccionado cuando su componente se carga, lo que ayuda al usuario a saber qué componente se está mostrando actualmente.</span><span class="sxs-lookup"><span data-stu-id="64ea7-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="64ea7-166">`_Imports.razor`: Engloba las directivas de Razor comunes que se van a incluir en los componentes de la aplicación (`.razor`), como las directivas [`@using`](xref:mvc/views/razor#using) de los espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="64ea7-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="64ea7-167">Carpeta `Data` (Blazor Server): contiene la clase `WeatherForecast` y la implementación de `WeatherForecastService`, que proporcionan datos meteorológicos de ejemplo al componente `FetchData` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="64ea7-168">`wwwroot`: carpeta [Web Root](xref:fundamentals/index#web-root) de la aplicación que contiene los recursos estáticos públicos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="64ea7-169">`appsettings.json`: Contiene las [opciones de configuración](xref:blazor/fundamentals/configuration) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ea7-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="64ea7-170">En una aplicación Blazor WebAssembly, el archivo de configuración de la aplicación se encuentra en la carpeta `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="64ea7-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="64ea7-171">En una aplicación Blazor Server, el archivo de configuración de la aplicación se encuentra en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="64ea7-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
