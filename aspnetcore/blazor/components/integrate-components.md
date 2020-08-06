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
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a>Integración de componentes de Razor de ASP.NET Core en aplicaciones de Razor Pages y MVC

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC. Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.

Después de [preparar la aplicación](#prepare-the-app), use las instrucciones que aparecen en las secciones siguientes en función de los requisitos de la aplicación:

* Componentes enrutables: para los componentes que se pueden enrutar directamente desde las solicitudes del usuario. Siga estas instrucciones cuando los visitantes puedan hacer una solicitud HTTP en el explorador para un componente con una directiva [`@page`](xref:mvc/views/razor#page).
  * [Uso de componentes enrutables en una aplicación Razor Pages](#use-routable-components-in-a-razor-pages-app).
  * [Uso de componentes enrutables en una aplicación MVC](#use-routable-components-in-an-mvc-app).
* [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view): para los componentes que no se pueden enrutar directamente desde las solicitudes del usuario. Siga estas instrucciones cuando la aplicación inserte componentes en páginas y vistas existentes con el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Preparación de la aplicación

Una aplicación Razor Pages o MVC existente puede integrar componentes Razor en páginas y vistas:

1. En el archivo de diseño de la aplicación (`_Layout.cshtml`):

   * Agregue la etiqueta `<base>` siguiente al elemento `<head>`:

     ```html
     <base href="~/" />
     ```

     El valor `href` (la *ruta de acceso base de la aplicación*) del ejemplo anterior da por hecho que la aplicación reside en la ruta de acceso URL raíz (`/`). Si la aplicación es una subaplicación, siga las instrucciones de la sección *Ruta de acceso base de la aplicación* del artículo <xref:blazor/host-and-deploy/index#app-base-path>.

     El archivo `_Layout.cshtml` se encuentra en la carpeta *Pages/Shared* de una aplicación Razor Pages o en la carpeta *Views/Shared* de una aplicación MVC.

   * Agregue una etiqueta `<script>` para el script *blazor.server.js* inmediatamente antes de la etiqueta `</body>` de cierre:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     El marco agrega el script *blazor.server.js* a la aplicación. No es necesario agregar manualmente el script a la aplicación.

1. Agregue un archivo `_Imports.razor` a la carpeta raíz del proyecto con el contenido siguiente (cambie el último espacio de nombres, `MyAppNamespace`, por el espacio de nombres de la aplicación):

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

1. En `Startup.ConfigureServices`, registre el servicio de Blazor Server:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. En `Startup.Configure`, agregue el punto de conexión de Blazor Hub a `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre los componentes en cualquier página o vista. Para obtener más información, vea la sección [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view).

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Uso de componentes enrutables en una aplicación Razor Pages

*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*

Para admitir componentes Razor enrutables en aplicaciones Razor Pages:

1. Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).

1. Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:

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

1. Agregue un archivo `_Host.cshtml` a la carpeta `Pages` con el contenido siguiente:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:

   * Se representa previamente en la página.
   * Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

   | Modo de representación | Descripción |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server. La salida del componente `App` no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente `App` en HTML estático. |

   Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Agregue una ruta de prioridad baja para la página `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Agregue componentes enrutables a la aplicación. Por ejemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).

## <a name="use-routable-components-in-an-mvc-app"></a>Uso de componentes enrutables en una aplicación MVC

*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*

Para admitir componentes Razor enrutables en aplicaciones MVC, haga lo siguiente:

1. Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).

1. Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:

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

1. Agregue un archivo `_Host.cshtml` a la carpeta `Views/Home` con el contenido siguiente:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:

   * Se representa previamente en la página.
   * Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

   | Modo de representación | Descripción |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server. La salida del componente `App` no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente `App` en HTML estático. |

   Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Agregue una acción al controlador de inicio:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Agregue una ruta de prioridad baja para la acción de controlador que devuelve la vista `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Cree una carpeta `Pages` y agregue componentes enrutables a la aplicación. Por ejemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).

## <a name="render-components-from-a-page-or-view"></a>Representación de componentes a partir de una página o vista

*Esta sección pertenece a la adición de componentes a páginas o vistas, donde los componentes no son enrutables directamente desde las solicitudes del usuario.*

Para representar un componente a partir de una página o vista, use el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Representación de componentes interactivos con estado

Los componentes interactivos con estado se pueden agregar a una página de Razor o una vista.

Cuando se representa la página o la vista:

* El componente se representa previamente con la página o la vista.
* Se pierde el estado inicial del componente que se usa para la representación previa.
* Cuando se establece la conexión SignalR, se crea un estado del componente.

La siguiente página de Razor representa un componente `Counter`:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Representación de componentes no interactivos

En la siguiente página de Razor, el componente `Counter` se representa de forma estática con un valor inicial que se especifica mediante un formulario. Como el componente se representa de forma estática, no es interactivo:

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

Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Espacios de nombres de componentes

Si usa una carpeta personalizada para contener los componentes de la aplicación, agregue el espacio de nombres que representa la carpeta a la página o vista, o bien al archivo `_ViewImports.cshtml`. En el ejemplo siguiente:

* Cambie `MyAppNamespace` en el espacio de nombres de la aplicación.
* Si no se usa una carpeta denominada *Components* para contener los componentes, cambie `Components` en la carpeta donde estos se encuentren.

```cshtml
@using MyAppNamespace.Components
```

El archivo `_ViewImports.cshtml` se encuentra en la carpeta `Pages` de una aplicación Razor Pages o en la carpeta `Views` de una aplicación de MVC.

Para obtener más información, vea <xref:blazor/components/index#namespaces>.
