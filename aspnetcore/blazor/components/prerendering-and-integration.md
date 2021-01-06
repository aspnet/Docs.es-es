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
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="3c24a-103">Integración y representación previa de componentes Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c24a-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="3c24a-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3c24a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3c24a-105">Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC en una solución Blazor WebAssembly hospedada.</span><span class="sxs-lookup"><span data-stu-id="3c24a-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="3c24a-106">Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="3c24a-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="3c24a-107">Configuración</span><span class="sxs-lookup"><span data-stu-id="3c24a-107">Configuration</span></span>

<span data-ttu-id="3c24a-108">Para configurar la representación previa de una aplicación Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="3c24a-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="3c24a-109">Hospede la aplicación Blazor WebAssembly en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c24a-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="3c24a-110">Se puede agregar una aplicación Blazor WebAssembly independiente a una solución de ASP.NET Core, o bien se puede usar una aplicación Blazor WebAssembly hospedada creada a partir de la plantilla de proyecto de Blazor hospedada.</span><span class="sxs-lookup"><span data-stu-id="3c24a-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="3c24a-111">Quite el archivo `wwwroot/index.html` estático predeterminado del proyecto de cliente Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3c24a-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="3c24a-112">Elimine la siguiente línea en `Program.Main` en el proyecto de cliente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="3c24a-113">Agregue un archivo `Pages/_Host.cshtml` al proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="3c24a-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="3c24a-114">Puede obtener un archivo `_Host.cshtml` de una aplicación creada a partir de la plantilla Blazor Server con el comando `dotnet new blazorserver -o BlazorServer` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="3c24a-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="3c24a-115">Después de colocar el archivo `Pages/_Host.cshtml` en la aplicación de servidor de la solución Blazor WebAssembly hospedada, realice los cambios siguientes en el archivo:</span><span class="sxs-lookup"><span data-stu-id="3c24a-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="3c24a-116">Establezca el espacio de nombres en la carpeta `Pages` de la aplicación de servidor (por ejemplo, `@namespace BlazorHosted.Server.Pages`).</span><span class="sxs-lookup"><span data-stu-id="3c24a-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="3c24a-117">Establezca una directiva [`@using`](xref:mvc/views/razor#using) para el proyecto de cliente (por ejemplo, `@using BlazorHosted.Client`).</span><span class="sxs-lookup"><span data-stu-id="3c24a-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="3c24a-118">Actualice los vínculos de la hoja de estilos para que apunten a la hoja de estilos de la aplicación WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3c24a-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="3c24a-119">En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="3c24a-120">Actualice el objeto `render-mode` de la [aplicación auxiliar de etiquetas de componentes](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para representar previamente el componente `App` raíz:</span><span class="sxs-lookup"><span data-stu-id="3c24a-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="3c24a-121">Actualice el origen del script de Blazor para usar el script de Blazor WebAssembly del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="3c24a-122">En `Startup.Configure` (`Startup.cs`) del proyecto de servidor:</span><span class="sxs-lookup"><span data-stu-id="3c24a-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="3c24a-123">Llame a `UseDeveloperExceptionPage` en el generador de aplicaciones en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="3c24a-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="3c24a-124">Llame a `UseBlazorFrameworkFiles` en el generador de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3c24a-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="3c24a-125">Cambie la reserva de la página `index.html` (`endpoints.MapFallbackToFile("index.html");`) a la página `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="3c24a-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

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

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="3c24a-126">Representación de componentes en una página o vista con la aplicación auxiliar de etiquetas de componentes</span><span class="sxs-lookup"><span data-stu-id="3c24a-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="3c24a-127">La aplicación auxiliar de etiquetas de componentes admite dos modos de representación para representar un componente de una aplicación Blazor WebAssembly en una página o vista:</span><span class="sxs-lookup"><span data-stu-id="3c24a-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="3c24a-128">`WebAssembly`: representa un marcador para una aplicación Blazor WebAssembly, a fin de utilizarlo para incluir un componente interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="3c24a-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="3c24a-129">El componente no está representado previamente.</span><span class="sxs-lookup"><span data-stu-id="3c24a-129">The component isn't prerendered.</span></span> <span data-ttu-id="3c24a-130">Esta opción facilita la representación de diferentes componentes Blazor WebAssembly en páginas distintas.</span><span class="sxs-lookup"><span data-stu-id="3c24a-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="3c24a-131">`WebAssemblyPrerendered`: representa previamente el componente en HTML estático e incluye un marcador para una aplicación Blazor WebAssembly, a fin de utilizarlo para que el componente sea interactivo cuando se carga en el explorador.</span><span class="sxs-lookup"><span data-stu-id="3c24a-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="3c24a-132">En el siguiente ejemplo de Razor Pages, el componente `Counter` se representa en una página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="3c24a-133">Para convertir el componente en interactivo, el script Blazor WebAssembly se incluye en la [sección de representación](xref:mvc/views/layout#sections) de la página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="3c24a-134">Para evitar el uso del espacio de nombres completo para el componente `Counter` con la aplicación auxiliar de etiquetas de componentes (`{APP ASSEMBLY}.Pages.Counter`), agregue una directiva [`@using`](xref:mvc/views/razor#using) para el espacio de nombres `Pages` de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="3c24a-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="3c24a-135">En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="3c24a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3c24a-137">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="3c24a-138">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="3c24a-139">Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="3c24a-140">En el ejemplo anterior se requiere que el diseño de la aplicación del servidor (`_Layout.cshtml`) incluya una [sección de representación](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) para el script dentro de la etiqueta `</body>` de cierre:</span><span class="sxs-lookup"><span data-stu-id="3c24a-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="3c24a-141">El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="3c24a-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="3c24a-142">Si la aplicación también debe dar estilo a los componentes con los estilos en la aplicación Blazor WebAssembly, incluya los estilos de la aplicación en el archivo `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="3c24a-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="3c24a-143">En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="3c24a-144">Representación de componentes en una página o vista con un selector de CSS</span><span class="sxs-lookup"><span data-stu-id="3c24a-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="3c24a-145">Agregue los componentes raíz al proyecto *Client* en `Program.Main` (`Program.cs`).</span><span class="sxs-lookup"><span data-stu-id="3c24a-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="3c24a-146">En el siguiente ejemplo, el componente `Counter` se declara como un componente raíz con un selector de CSS que selecciona el elemento con el objeto `id` que coincide con `my-counter`.</span><span class="sxs-lookup"><span data-stu-id="3c24a-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="3c24a-147">En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="3c24a-148">En el siguiente ejemplo de Razor Pages, el componente `Counter` se representa en una página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="3c24a-149">Para convertir el componente en interactivo, el script Blazor WebAssembly se incluye en la [sección de representación](xref:mvc/views/layout#sections) de la página:</span><span class="sxs-lookup"><span data-stu-id="3c24a-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="3c24a-150">En el ejemplo anterior se requiere que el diseño de la aplicación del servidor (`_Layout.cshtml`) incluya una [sección de representación](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) para el script dentro de la etiqueta `</body>` de cierre:</span><span class="sxs-lookup"><span data-stu-id="3c24a-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="3c24a-151">El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="3c24a-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="3c24a-152">Si la aplicación también debe dar estilo a los componentes con los estilos en la aplicación Blazor WebAssembly, incluya los estilos de la aplicación en el archivo `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="3c24a-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="3c24a-153">En el ejemplo siguiente, el espacio de nombres de la aplicación cliente es `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3c24a-154">La integración de componentes Razor en aplicaciones de Razor Pages y MVC en una solución Blazor WebAssembly hospedada es compatible con ASP.NET Core en .NET 5 o versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="3c24a-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="3c24a-155">Seleccione una versión .NET 5 o posterior de este artículo.</span><span class="sxs-lookup"><span data-stu-id="3c24a-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="3c24a-156">Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC en una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="3c24a-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="3c24a-157">Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="3c24a-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="3c24a-158">Después de [configurar la aplicación](#configuration), use las instrucciones que aparecen en las secciones siguientes en función de los requisitos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="3c24a-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="3c24a-159">Componentes enrutables: para los componentes que se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="3c24a-160">Siga estas instrucciones cuando los visitantes puedan hacer una solicitud HTTP en el explorador para un componente con una directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="3c24a-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="3c24a-161">[Uso de componentes enrutables en una aplicación Razor Pages](#use-routable-components-in-a-razor-pages-app).</span><span class="sxs-lookup"><span data-stu-id="3c24a-161">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * <span data-ttu-id="3c24a-162">[Uso de componentes enrutables en una aplicación MVC](#use-routable-components-in-an-mvc-app).</span><span class="sxs-lookup"><span data-stu-id="3c24a-162">[Use routable components in an MVC app](#use-routable-components-in-an-mvc-app)</span></span>
* <span data-ttu-id="3c24a-163">[Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view): para los componentes que no se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="3c24a-164">Siga estas instrucciones cuando la aplicación inserte componentes en páginas y vistas existentes con el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="3c24a-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="3c24a-165">Configuración</span><span class="sxs-lookup"><span data-stu-id="3c24a-165">Configuration</span></span>

<span data-ttu-id="3c24a-166">Una aplicación Razor Pages o MVC existente puede integrar componentes Razor en páginas y vistas:</span><span class="sxs-lookup"><span data-stu-id="3c24a-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="3c24a-167">En el archivo de diseño de la aplicación (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="3c24a-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="3c24a-168">Agregue la etiqueta `<base>` siguiente al elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="3c24a-169">El valor `href` (la *ruta de acceso base de la aplicación*) del ejemplo anterior da por hecho que la aplicación reside en la ruta de acceso URL raíz (`/`).</span><span class="sxs-lookup"><span data-stu-id="3c24a-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="3c24a-170">Si la aplicación es una subaplicación, siga las instrucciones de la sección *Ruta de acceso base de la aplicación* del artículo <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="3c24a-171">El archivo `_Layout.cshtml` se encuentra en la carpeta `Pages/Shared` de una aplicación Razor Pages o en la carpeta `Views/Shared` de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="3c24a-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="3c24a-172">Agregue una etiqueta `<script>` para el script `blazor.server.js` inmediatamente antes de la sección de representación de `Scripts`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="3c24a-173">El marco agrega el script `blazor.server.js` a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c24a-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="3c24a-174">No es necesario agregar manualmente el script a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c24a-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="3c24a-175">Agregue un archivo `_Imports.razor` a la carpeta raíz del proyecto con el contenido siguiente (cambie el último espacio de nombres, `MyAppNamespace`, por el espacio de nombres de la aplicación):</span><span class="sxs-lookup"><span data-stu-id="3c24a-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="3c24a-176">En `Startup.ConfigureServices`, registre el servicio de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="3c24a-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="3c24a-177">En `Startup.Configure`, agregue el punto de conexión de Blazor Hub a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="3c24a-178">Integre los componentes en cualquier página o vista.</span><span class="sxs-lookup"><span data-stu-id="3c24a-178">Integrate components into any page or view.</span></span> <span data-ttu-id="3c24a-179">Para obtener más información, vea la sección [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="3c24a-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="3c24a-180">Uso de componentes enrutables en una aplicación Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3c24a-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="3c24a-181">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="3c24a-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="3c24a-182">Para admitir componentes Razor enrutables en aplicaciones Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="3c24a-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="3c24a-183">Siga las instrucciones de la sección [Configuración](#configuration).</span><span class="sxs-lookup"><span data-stu-id="3c24a-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="3c24a-184">Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-184">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="3c24a-185">Agregue un archivo `_Host.cshtml` a la carpeta `Pages` con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="3c24a-186">Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.</span><span class="sxs-lookup"><span data-stu-id="3c24a-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="3c24a-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="3c24a-188">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="3c24a-189">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="3c24a-190">Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="3c24a-191">Agregue una ruta de prioridad baja para la página `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="3c24a-192">Agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c24a-192">Add routable components to the app.</span></span> <span data-ttu-id="3c24a-193">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3c24a-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="3c24a-194">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="3c24a-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="3c24a-195">Uso de componentes enrutables en una aplicación MVC</span><span class="sxs-lookup"><span data-stu-id="3c24a-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="3c24a-196">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="3c24a-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="3c24a-197">Para admitir componentes Razor enrutables en aplicaciones MVC, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="3c24a-198">Siga las instrucciones de la sección [Configuración](#configuration).</span><span class="sxs-lookup"><span data-stu-id="3c24a-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="3c24a-199">Agregue un archivo `App.razor` a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="3c24a-200">Agregue un archivo `_Host.cshtml` a la carpeta `Views/Home` con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="3c24a-201">Los componentes usan el archivo compartido `_Layout.cshtml` para su diseño.</span><span class="sxs-lookup"><span data-stu-id="3c24a-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="3c24a-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="3c24a-203">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="3c24a-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="3c24a-204">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="3c24a-205">Para obtener más información sobre la aplicación auxiliar de etiquetas de componentes, como pasar parámetros y configurar <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="3c24a-206">Agregue una acción al controlador de inicio:</span><span class="sxs-lookup"><span data-stu-id="3c24a-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="3c24a-207">Agregue una ruta de prioridad baja para la acción de controlador que devuelve la vista `_Host.cshtml` a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="3c24a-208">Cree una carpeta `Pages` y agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c24a-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="3c24a-209">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3c24a-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="3c24a-210">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="3c24a-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="3c24a-211">Representación de componentes a partir de una página o vista</span><span class="sxs-lookup"><span data-stu-id="3c24a-211">Render components from a page or view</span></span>

<span data-ttu-id="3c24a-212">*Esta sección pertenece a la adición de componentes a páginas o vistas, donde los componentes no son enrutables directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="3c24a-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="3c24a-213">Para representar un componente a partir de una página o vista, use el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="3c24a-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="3c24a-214">Representación de componentes interactivos con estado</span><span class="sxs-lookup"><span data-stu-id="3c24a-214">Render stateful interactive components</span></span>

<span data-ttu-id="3c24a-215">Los componentes interactivos con estado se pueden agregar a una página de Razor o una vista.</span><span class="sxs-lookup"><span data-stu-id="3c24a-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="3c24a-216">Cuando se representa la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="3c24a-216">When the page or view renders:</span></span>

* <span data-ttu-id="3c24a-217">El componente se representa previamente con la página o la vista.</span><span class="sxs-lookup"><span data-stu-id="3c24a-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="3c24a-218">Se pierde el estado inicial del componente que se usa para la representación previa.</span><span class="sxs-lookup"><span data-stu-id="3c24a-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="3c24a-219">Cuando se establece la conexión SignalR, se crea un estado del componente.</span><span class="sxs-lookup"><span data-stu-id="3c24a-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="3c24a-220">La siguiente página de Razor representa un componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="3c24a-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="3c24a-221">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="3c24a-222">Representación de componentes no interactivos</span><span class="sxs-lookup"><span data-stu-id="3c24a-222">Render noninteractive components</span></span>

<span data-ttu-id="3c24a-223">En la siguiente página de Razor, el componente `Counter` se representa de forma estática con un valor inicial que se especifica mediante un formulario.</span><span class="sxs-lookup"><span data-stu-id="3c24a-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="3c24a-224">Como el componente se representa de forma estática, no es interactivo:</span><span class="sxs-lookup"><span data-stu-id="3c24a-224">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="3c24a-225">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="3c24a-226">Espacios de nombres de componentes</span><span class="sxs-lookup"><span data-stu-id="3c24a-226">Component namespaces</span></span>

<span data-ttu-id="3c24a-227">Si usa una carpeta personalizada para contener los componentes de la aplicación, agregue el espacio de nombres que representa la carpeta a la página o vista, o bien al archivo `_ViewImports.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="3c24a-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="3c24a-228">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c24a-228">In the following example:</span></span>

* <span data-ttu-id="3c24a-229">Cambie `MyAppNamespace` en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c24a-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="3c24a-230">Si no se usa una carpeta denominada `Components` para contener los componentes, cambie `Components` en la carpeta donde estos se encuentren.</span><span class="sxs-lookup"><span data-stu-id="3c24a-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="3c24a-231">El archivo `_ViewImports.cshtml` se encuentra en la carpeta `Pages` de una aplicación Razor Pages o en la carpeta `Views` de una aplicación de MVC.</span><span class="sxs-lookup"><span data-stu-id="3c24a-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="3c24a-232">Para obtener más información, vea <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="3c24a-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
