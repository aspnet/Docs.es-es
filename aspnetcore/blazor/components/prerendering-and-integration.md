---
title: Integración y representación previa de componentes Razor de ASP.NET Core
author: guardrex
description: Obtenga información sobre los escenarios de integración de componentes Razor para aplicaciones Blazor, incluida la representación previa de componentes Razor en el servidor.
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506986"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>Integración y representación previa de componentes Razor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC en una solución Blazor WebAssembly hospedada. Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.

## <a name="configuration"></a>Configuración

Para configurar la representación previa de una aplicación Blazor WebAssembly:

1. Hospede la aplicación Blazor WebAssembly en una aplicación ASP.NET Core. Se puede agregar una aplicación Blazor WebAssembly independiente a una solución de ASP.NET Core, o bien se puede usar una aplicación Blazor WebAssembly hospedada creada a partir de la plantilla de proyecto de Blazor hospedada.

1. Quite el archivo `wwwroot/index.html` estático predeterminado del proyecto de cliente Blazor WebAssembly.

1. Elimine la siguiente línea en `Program.Main` en el proyecto de cliente:

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Agregue un archivo `Pages/_Host.cshtml` al proyecto de servidor. Puede obtener un archivo `_Host.cshtml` de una aplicación creada a partir de la plantilla Blazor Server con el comando `dotnet new blazorserver -o BlazorServer` en un shell de comandos. Después de colocar el archivo `Pages/_Host.cshtml` en la aplicación de servidor de la solución Blazor WebAssembly hospedada, realice los cambios siguientes en el archivo:

   * Establezca el espacio de nombres en la carpeta `Pages` de la aplicación de servidor (por ejemplo, `@namespace BlazorHosted.Server.Pages`).
   * Establezca una directiva [`@using`](xref:mvc/views/razor#using) para el proyecto de cliente (por ejemplo, `@using BlazorHosted.Client`).
   * Actualice los vínculos de la hoja de estilos para que apunten a la hoja de estilos de la aplicación WebAssembly. En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Actualice el objeto `render-mode` de la [aplicación auxiliar de etiquetas de componentes](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para representar previamente el componente `App` raíz:

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Actualice el origen del script de Blazor para usar el script de Blazor WebAssembly del lado cliente:

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. En `Startup.Configure` (`Startup.cs`) del proyecto de servidor:

   * Llame a `UseDeveloperExceptionPage` en el generador de aplicaciones en el entorno de desarrollo.
   * Llame a `UseBlazorFrameworkFiles` en el generador de aplicaciones.
   * Cambie la reserva de la página `index.html` (`endpoints.MapFallbackToFile("index.html");`) a la página `_Host.cshtml`.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Representación de componentes en una página o vista con la aplicación auxiliar de etiquetas de componentes

La aplicación auxiliar de etiquetas de componentes admite dos modos de representación para representar un componente de una aplicación Blazor WebAssembly en una página o vista:

* `WebAssembly`: representa un marcador para una aplicación Blazor WebAssembly, a fin de utilizarlo para incluir un componente interactivo cuando se carga en el explorador. El componente no está representado previamente. Esta opción facilita la representación de diferentes componentes Blazor WebAssembly en páginas distintas.
* `WebAssemblyPrerendered`: representa previamente el componente en HTML estático e incluye un marcador para una aplicación Blazor WebAssembly, a fin de utilizarlo para que el componente sea interactivo cuando se carga en el explorador.

En el siguiente ejemplo de Razor Pages, el componente `Counter` se representa en una página. Para convertir el componente en interactivo, el script Blazor WebAssembly se incluye en la [sección de representación](xref:mvc/views/layout#sections) de la página. Para evitar el uso del espacio de nombres completo para el componente `Counter` con la aplicación auxiliar de etiquetas de componentes (`{APP ASSEMBLY}.Pages.Counter`), agregue una directiva [`@using`](xref:mvc/views/razor#using) para el espacio de nombres `Pages` de la aplicación cliente. En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:

* Se representa previamente en la página.
* Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

En el ejemplo anterior se requiere que el diseño de la aplicación del servidor (`_Layout.cshtml`) incluya una [sección de representación](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) para el script dentro de la etiqueta `</body>` de cierre:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.

Si la aplicación también debe dar estilo a los componentes con los estilos en la aplicación Blazor WebAssembly, incluya los estilos de la aplicación en el archivo `_Layout.cshtml`. En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Representación de componentes en una página o vista con un selector de CSS

Agregue los componentes raíz al proyecto *Client* en `Program.Main` (`Program.cs`). En el siguiente ejemplo, el componente `Counter` se declara como un componente raíz con un selector de CSS que selecciona el elemento con el objeto `id` que coincide con `my-counter`. En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

En el siguiente ejemplo de Razor Pages, el componente `Counter` se representa en una página. Para convertir el componente en interactivo, el script Blazor WebAssembly se incluye en la [sección de representación](xref:mvc/views/layout#sections) de la página:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

En el ejemplo anterior se requiere que el diseño de la aplicación del servidor (`_Layout.cshtml`) incluya una [sección de representación](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) para el script dentro de la etiqueta `</body>` de cierre:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.

Si la aplicación también debe dar estilo a los componentes con los estilos en la aplicación Blazor WebAssembly, incluya los estilos de la aplicación en el archivo `_Layout.cshtml`. En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

La integración de componentes Razor en aplicaciones de Razor Pages y MVC en una solución Blazor WebAssembly hospedada es compatible con ASP.NET Core en .NET 5 o versiones posteriores. Seleccione una versión .NET 5 o posterior de este artículo.

::: moniker-end

::: zone-end

::: zone pivot="server"

Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC en una aplicación Blazor Server. Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.

Después de [configurar la aplicación](#configuration), use las instrucciones que aparecen en las secciones siguientes en función de los requisitos de la aplicación:

* Componentes enrutables: para los componentes que se pueden enrutar directamente desde las solicitudes del usuario. Siga estas instrucciones cuando los visitantes puedan hacer una solicitud HTTP en el explorador para un componente con una directiva [`@page`](xref:mvc/views/razor#page).
  * [Uso de componentes enrutables en una aplicación Razor Pages](#use-routable-components-in-a-razor-pages-app).
  * [Uso de componentes enrutables en una aplicación MVC](#use-routable-components-in-an-mvc-app).
* [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view): para los componentes que no se pueden enrutar directamente desde las solicitudes del usuario. Siga estas instrucciones cuando la aplicación inserte componentes en páginas y vistas existentes con el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="configuration"></a>Configuración

Una aplicación Razor Pages o MVC existente puede integrar componentes Razor en páginas y vistas:

1. En el archivo de diseño de la aplicación (`_Layout.cshtml`):

   * Agregue la etiqueta `<base>` siguiente al elemento `<head>`:

     ```html
     <base href="~/" />
     ```

     El valor `href` (la *ruta de acceso base de la aplicación*) del ejemplo anterior da por hecho que la aplicación reside en la ruta de acceso URL raíz (`/`). Si la aplicación es una subaplicación, siga las instrucciones de la sección *Ruta de acceso base de la aplicación* del artículo <xref:blazor/host-and-deploy/index#app-base-path>.

     El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.

   * Agregue una etiqueta `<script>` para el script `blazor.server.js` inmediatamente antes de la sección de representación de `Scripts`:

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     El marco agrega el script `blazor.server.js` a la aplicación. No es necesario agregar manualmente el script a la aplicación.

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

1. Siga las instrucciones de la sección [Configuración](#configuration).

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

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

1. Siga las instrucciones de la sección [Configuración](#configuration).

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

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
* Si no se usa una carpeta denominada `Components` para contener los componentes, cambie `Components` en la carpeta donde estos se encuentren.

```cshtml
@using MyAppNamespace.Components
```

El archivo `_ViewImports.cshtml` se encuentra en la carpeta `Pages` de una aplicación Razor Pages o en la carpeta `Views` de una aplicación de MVC.

Para obtener más información, vea <xref:blazor/components/index#namespaces>.

::: zone-end
