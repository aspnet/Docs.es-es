---
title: Integración de componentes de Razor de ASP.NET Core en aplicaciones de Razor Pages y MVC
author: guardrex
description: Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: a28c43ed68adb7af9c30f7ae62872811361d8f9c
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819098"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="a8181-103">Integración de componentes de Razor de ASP.NET Core en aplicaciones de Razor Pages y MVC</span><span class="sxs-lookup"><span data-stu-id="a8181-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="a8181-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a8181-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a8181-105">Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC.</span><span class="sxs-lookup"><span data-stu-id="a8181-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="a8181-106">Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="a8181-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="a8181-107">Después de [preparar la aplicación](#prepare-the-app), use las instrucciones que aparecen en las secciones siguientes en función de los requisitos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a8181-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="a8181-108">Componentes enrutables: para los componentes que se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="a8181-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="a8181-109">Siga estas instrucciones cuando los visitantes puedan hacer una solicitud HTTP en el explorador para un componente con una directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="a8181-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="a8181-110">[Uso de componentes enrutables en una aplicación Razor Pages](#use-routable-components-in-a-razor-pages-app).</span><span class="sxs-lookup"><span data-stu-id="a8181-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * <span data-ttu-id="a8181-111">[Uso de componentes enrutables en una aplicación MVC](#use-routable-components-in-an-mvc-app).</span><span class="sxs-lookup"><span data-stu-id="a8181-111">[Use routable components in an MVC app](#use-routable-components-in-an-mvc-app)</span></span>
* <span data-ttu-id="a8181-112">[Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view): para los componentes que no se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="a8181-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="a8181-113">Siga estas instrucciones cuando la aplicación inserte componentes en páginas y vistas existentes con el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8181-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="a8181-114">Preparación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="a8181-114">Prepare the app</span></span>

<span data-ttu-id="a8181-115">Una aplicación Razor Pages o MVC existente puede integrar componentes Razor en páginas y vistas:</span><span class="sxs-lookup"><span data-stu-id="a8181-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="a8181-116">En el archivo de diseño de la aplicación (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="a8181-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="a8181-117">Agregue la etiqueta `<base>` siguiente al elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="a8181-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="a8181-118">El valor `href` (la *ruta de acceso base de la aplicación*) del ejemplo anterior da por hecho que la aplicación reside en la ruta de acceso URL raíz (`/`).</span><span class="sxs-lookup"><span data-stu-id="a8181-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="a8181-119">Si la aplicación es una subaplicación, siga las instrucciones de la sección *Ruta de acceso base de la aplicación* del artículo <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="a8181-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="a8181-120">El archivo `_Layout.cshtml` se encuentra en la carpeta *Pages/Shared* de una aplicación Razor Pages o en la carpeta *Views/Shared* de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="a8181-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="a8181-121">Agregue una etiqueta `<script>` para el script *blazor.server.js* inmediatamente antes de la etiqueta `</body>` de cierre:</span><span class="sxs-lookup"><span data-stu-id="a8181-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="a8181-122">El marco agrega el script *blazor.server.js* a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8181-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="a8181-123">No es necesario agregar manualmente el script a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8181-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="a8181-124">Agregue un archivo `_Imports.razor` a la carpeta raíz del proyecto con el contenido siguiente (cambie el último espacio de nombres, `MyAppNamespace`, por el espacio de nombres de la aplicación):</span><span class="sxs-lookup"><span data-stu-id="a8181-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="a8181-125">En `Startup.ConfigureServices`, registre el servicio de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="a8181-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="a8181-126">En `Startup.Configure`, agregue el punto de conexión de Blazor Hub a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="a8181-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="a8181-127">Integre los componentes en cualquier página o vista.</span><span class="sxs-lookup"><span data-stu-id="a8181-127">Integrate components into any page or view.</span></span> <span data-ttu-id="a8181-128">Para obtener más información, vea la sección [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="a8181-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="a8181-129">Uso de componentes enrutables en una aplicación Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a8181-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="a8181-130">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="a8181-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="a8181-131">Para admitir componentes Razor enrutables en aplicaciones Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="a8181-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="a8181-132">Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="a8181-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="a8181-133">Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="a8181-134">Agregue un archivo `_Host.cshtml` a la carpeta `Pages` con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="a8181-135">Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.</span><span class="sxs-lookup"><span data-stu-id="a8181-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="a8181-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="a8181-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="a8181-137">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="a8181-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="a8181-138">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="a8181-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="a8181-139">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="a8181-139">Render Mode</span></span> | <span data-ttu-id="a8181-140">Descripción</span><span class="sxs-lookup"><span data-stu-id="a8181-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a8181-141">Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8181-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8181-142">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a8181-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a8181-143">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8181-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8181-144">La salida del componente `App` no está incluida.</span><span class="sxs-lookup"><span data-stu-id="a8181-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="a8181-145">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a8181-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a8181-146">Representa el componente `App` en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a8181-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="a8181-147">Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="a8181-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="a8181-148">Agregue una ruta de prioridad baja para la página `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8181-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="a8181-149">Agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8181-149">Add routable components to the app.</span></span> <span data-ttu-id="a8181-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a8181-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="a8181-151">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="a8181-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="a8181-152">Uso de componentes enrutables en una aplicación MVC</span><span class="sxs-lookup"><span data-stu-id="a8181-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="a8181-153">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="a8181-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="a8181-154">Para admitir componentes Razor enrutables en aplicaciones MVC, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="a8181-155">Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="a8181-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="a8181-156">Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="a8181-157">Agregue un archivo `_Host.cshtml` a la carpeta `Views/Home` con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="a8181-158">Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.</span><span class="sxs-lookup"><span data-stu-id="a8181-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="a8181-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="a8181-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="a8181-160">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="a8181-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="a8181-161">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="a8181-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="a8181-162">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="a8181-162">Render Mode</span></span> | <span data-ttu-id="a8181-163">Descripción</span><span class="sxs-lookup"><span data-stu-id="a8181-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a8181-164">Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8181-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8181-165">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a8181-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a8181-166">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8181-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8181-167">La salida del componente `App` no está incluida.</span><span class="sxs-lookup"><span data-stu-id="a8181-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="a8181-168">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="a8181-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a8181-169">Representa el componente `App` en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a8181-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="a8181-170">Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="a8181-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="a8181-171">Agregue una acción al controlador de inicio:</span><span class="sxs-lookup"><span data-stu-id="a8181-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="a8181-172">Agregue una ruta de prioridad baja para la acción de controlador que devuelve la vista `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8181-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="a8181-173">Cree una carpeta `Pages` y agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8181-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="a8181-174">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a8181-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="a8181-175">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="a8181-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="a8181-176">Representación de componentes a partir de una página o vista</span><span class="sxs-lookup"><span data-stu-id="a8181-176">Render components from a page or view</span></span>

<span data-ttu-id="a8181-177">*Esta sección pertenece a la adición de componentes a páginas o vistas, donde los componentes no son enrutables directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="a8181-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="a8181-178">Para representar un componente a partir de una página o vista, use el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8181-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="a8181-179">Representación de componentes interactivos con estado</span><span class="sxs-lookup"><span data-stu-id="a8181-179">Render stateful interactive components</span></span>

<span data-ttu-id="a8181-180">Los componentes interactivos con estado se pueden agregar a una página de Razor o una vista.</span><span class="sxs-lookup"><span data-stu-id="a8181-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="a8181-181">Cuando se representa la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="a8181-181">When the page or view renders:</span></span>

* <span data-ttu-id="a8181-182">El componente se representa previamente con la página o la vista.</span><span class="sxs-lookup"><span data-stu-id="a8181-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="a8181-183">Se pierde el estado inicial del componente que se usa para la representación previa.</span><span class="sxs-lookup"><span data-stu-id="a8181-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="a8181-184">Cuando se establece la conexión SignalR, se crea un estado del componente.</span><span class="sxs-lookup"><span data-stu-id="a8181-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="a8181-185">La siguiente página de Razor representa un componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="a8181-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="a8181-186">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="a8181-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="a8181-187">Representación de componentes no interactivos</span><span class="sxs-lookup"><span data-stu-id="a8181-187">Render noninteractive components</span></span>

<span data-ttu-id="a8181-188">En la siguiente página de Razor, el componente `Counter` se representa de forma estática con un valor inicial que se especifica mediante un formulario.</span><span class="sxs-lookup"><span data-stu-id="a8181-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="a8181-189">Como el componente se representa de forma estática, no es interactivo:</span><span class="sxs-lookup"><span data-stu-id="a8181-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="a8181-190">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="a8181-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="a8181-191">Espacios de nombres de componentes</span><span class="sxs-lookup"><span data-stu-id="a8181-191">Component namespaces</span></span>

<span data-ttu-id="a8181-192">Si usa una carpeta personalizada para contener los componentes de la aplicación, agregue el espacio de nombres que representa la carpeta a la página o vista, o bien al archivo `_ViewImports.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="a8181-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="a8181-193">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a8181-193">In the following example:</span></span>

* <span data-ttu-id="a8181-194">Cambie `MyAppNamespace` en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a8181-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="a8181-195">Si no se usa una carpeta denominada *Components* para contener los componentes, cambie `Components` en la carpeta donde estos se encuentren.</span><span class="sxs-lookup"><span data-stu-id="a8181-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="a8181-196">El archivo `_ViewImports.cshtml` se encuentra en la carpeta `Pages` de una aplicación Razor Pages o en la carpeta `Views` de una aplicación de MVC.</span><span class="sxs-lookup"><span data-stu-id="a8181-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="a8181-197">Para obtener más información, vea <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="a8181-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
