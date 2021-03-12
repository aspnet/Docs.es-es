---
title: Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación en ASP.net Core
author: rick-anderson
description: Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación en ASP.net Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 23bc1db6a184e7babe87e2d311a8ac4a59e78dd0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588365"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="706b5-103">Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación</span><span class="sxs-lookup"><span data-stu-id="706b5-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="706b5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="706b5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="706b5-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="706b5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="706b5-106">Un *elemento de aplicación* es una abstracción sobre los recursos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="706b5-107">Los elementos de aplicación permiten a ASP.NET Core detectar controladores, ver componentes, aplicaciones auxiliares de etiquetas, Razor páginas, orígenes de compilación de Razor, etc.</span><span class="sxs-lookup"><span data-stu-id="706b5-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="706b5-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> es un elemento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="706b5-109">`AssemblyPart` encapsula una referencia de ensamblado y expone los tipos y las referencias de compilación.</span><span class="sxs-lookup"><span data-stu-id="706b5-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="706b5-110">Los [proveedores de características](#fp) trabajan con los elementos de aplicación para rellenar las características de una aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="706b5-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="706b5-111">El caso de uso principal de los elementos de aplicación es configurar una aplicación para detectar (o evitar cargar) características de ASP.NET Core de un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="706b5-112">Por ejemplo, puede que desee compartir la funcionalidad común entre varias aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="706b5-113">Mediante el uso de elementos de aplicación, puede compartir un ensamblado (DLL) que contiene controladores, vistas, Razor páginas, orígenes de compilación de Razor, aplicaciones auxiliares de etiquetas y mucho más con varias aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="706b5-114">Es preferible compartir un ensamblado a duplicar el código en varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="706b5-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="706b5-115">Las aplicaciones ASP.NET Core cargan características de <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="706b5-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="706b5-116">La clase <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa un elemento de aplicación que está respaldado por un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="706b5-117">Carga de características de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="706b5-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="706b5-118">Use las clases <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> y <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> para detectar y cargar características de ASP.NET Core (controladores, componentes de vista, etc.).</span><span class="sxs-lookup"><span data-stu-id="706b5-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="706b5-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> realiza un seguimiento de los elementos de aplicación y los proveedores de características disponibles.</span><span class="sxs-lookup"><span data-stu-id="706b5-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="706b5-120">`ApplicationPartManager` se configura en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="706b5-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="706b5-121">El código siguiente proporciona un enfoque alternativo para configurar `ApplicationPartManager` mediante `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="706b5-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="706b5-122">Los dos ejemplos de código anteriores cargan `SharedController` de un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="706b5-123">`SharedController` no se encuentra en el proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="706b5-124">Vea la descarga de ejemplo de la [solución WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="706b5-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="706b5-125">Inclusión de vistas</span><span class="sxs-lookup"><span data-stu-id="706b5-125">Include views</span></span>

<span data-ttu-id="706b5-126">Use una [ Razor biblioteca de clases](xref:razor-pages/ui-class) para incluir vistas en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="706b5-127">Impedimento de la carga de los recursos</span><span class="sxs-lookup"><span data-stu-id="706b5-127">Prevent loading resources</span></span>

<span data-ttu-id="706b5-128">Los elementos de aplicación se pueden usar para *evitar* la carga de recursos en un ensamblado o ubicación en concreto.</span><span class="sxs-lookup"><span data-stu-id="706b5-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="706b5-129">Agregue o quite miembros de la colección <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar los recursos o hacer que estos estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="706b5-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="706b5-130">El orden de las entradas de la colección `ApplicationParts` es irrelevante.</span><span class="sxs-lookup"><span data-stu-id="706b5-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="706b5-131">Configure `ApplicationPartManager` antes de usarlo para configurar los servicios en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="706b5-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="706b5-132">Por ejemplo, configure `ApplicationPartManager` antes de invocar a `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="706b5-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="706b5-133">Llame a `Remove` en la colección `ApplicationParts` para quitar un recurso.</span><span class="sxs-lookup"><span data-stu-id="706b5-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="706b5-134">`ApplicationPartManager` incluye elementos para:</span><span class="sxs-lookup"><span data-stu-id="706b5-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="706b5-135">El ensamblado de la aplicación y los ensamblados dependientes.</span><span class="sxs-lookup"><span data-stu-id="706b5-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="706b5-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="706b5-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="706b5-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="706b5-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="706b5-138">Proveedores de características</span><span class="sxs-lookup"><span data-stu-id="706b5-138">Feature providers</span></span>

<span data-ttu-id="706b5-139">Los proveedores de características de la aplicación examinan los elementos de aplicación y proporcionan características para esos elementos.</span><span class="sxs-lookup"><span data-stu-id="706b5-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="706b5-140">Existen proveedores de características integrados para las siguientes características de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="706b5-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="706b5-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="706b5-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="706b5-142">Los proveedores de características heredan de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, donde `T` es el tipo de la característica.</span><span class="sxs-lookup"><span data-stu-id="706b5-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="706b5-143">Los proveedores de características se pueden implementar para cualquiera de los tipos de características enumerados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="706b5-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="706b5-144">El orden de los proveedores de características en `ApplicationPartManager.FeatureProviders` puede afectar al comportamiento en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="706b5-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="706b5-145">Los proveedores agregados posteriormente pueden reaccionar ante las acciones realizadas por los proveedores agregados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="706b5-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="706b5-146">muestra de las características disponibles</span><span class="sxs-lookup"><span data-stu-id="706b5-146">Display available features</span></span>

<span data-ttu-id="706b5-147">Las características disponibles para una aplicación se pueden enumerar solicitando `ApplicationPartManager` a través de la [inserción de dependencias](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="706b5-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="706b5-148">El [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) usa el código anterior para mostrar las características de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="706b5-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="706b5-149">Detección en elementos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="706b5-149">Discovery in application parts</span></span>

<span data-ttu-id="706b5-150">Los errores HTTP 404 no son frecuentes cuando se usan elementos de aplicaciones para el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="706b5-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="706b5-151">Normalmente, estos errores se producen porque falta un requisito esencial sobre cómo se detectan los elementos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="706b5-152">Si la aplicación devuelve un error HTTP 404, verifique que se cumplen los siguientes requisitos:</span><span class="sxs-lookup"><span data-stu-id="706b5-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="706b5-153">La configuración `applicationName` debe establecerse en el ensamblado raíz que se usa para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="706b5-154">El ensamblado raíz que se usa para la detección suele ser el ensamblado del punto de entrada.</span><span class="sxs-lookup"><span data-stu-id="706b5-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="706b5-155">El ensamblado raíz debe tener una referencia a las partes que se usan para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="706b5-156">La referencia puede ser directa o transitiva.</span><span class="sxs-lookup"><span data-stu-id="706b5-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="706b5-157">El ensamblado raíz debe hacer referencia al SDK web.</span><span class="sxs-lookup"><span data-stu-id="706b5-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="706b5-158">El marco tiene una lógica que marca atributos en el ensamblado raíz que se usan para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="706b5-159">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="706b5-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="706b5-160">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="706b5-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="706b5-161">Un *elemento de aplicación* es una abstracción sobre los recursos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="706b5-162">Los elementos de aplicación permiten a ASP.NET Core detectar controladores, ver componentes, aplicaciones auxiliares de etiquetas, Razor páginas, orígenes de compilación de Razor, etc.</span><span class="sxs-lookup"><span data-stu-id="706b5-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="706b5-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is un elemento de aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="706b5-164">`AssemblyPart` encapsula una referencia de ensamblado y expone los tipos y las referencias de compilación.</span><span class="sxs-lookup"><span data-stu-id="706b5-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="706b5-165">Los *proveedores de características* trabajan con los elementos de aplicación para rellenar las características de una aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="706b5-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="706b5-166">El caso de uso principal de los elementos de aplicación es configurar una aplicación para detectar (o evitar cargar) características de ASP.NET Core de un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="706b5-167">Por ejemplo, puede que desee compartir la funcionalidad común entre varias aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="706b5-168">Mediante el uso de elementos de aplicación, puede compartir un ensamblado (DLL) que contiene controladores, vistas, Razor páginas, orígenes de compilación de Razor, aplicaciones auxiliares de etiquetas y mucho más con varias aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="706b5-169">Es preferible compartir un ensamblado a duplicar el código en varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="706b5-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="706b5-170">Las aplicaciones ASP.NET Core cargan características de <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="706b5-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="706b5-171">La clase <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa un elemento de aplicación que está respaldado por un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="706b5-172">Carga de características de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="706b5-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="706b5-173">Use las clases `ApplicationPart` y `AssemblyPart` para detectar y cargar características de ASP.NET Core (controladores, componentes de vista, etc.).</span><span class="sxs-lookup"><span data-stu-id="706b5-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="706b5-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> realiza un seguimiento de los elementos de aplicación y los proveedores de características disponibles.</span><span class="sxs-lookup"><span data-stu-id="706b5-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="706b5-175">`ApplicationPartManager` se configura en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="706b5-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="706b5-176">El código siguiente proporciona un enfoque alternativo para configurar `ApplicationPartManager` mediante `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="706b5-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="706b5-177">Los dos ejemplos de código anteriores cargan `SharedController` de un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="706b5-178">`SharedController` no está en el proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="706b5-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="706b5-179">Vea la descarga de ejemplo de la [solución WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="706b5-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="706b5-180">Inclusión de vistas</span><span class="sxs-lookup"><span data-stu-id="706b5-180">Include views</span></span>

<span data-ttu-id="706b5-181">Use una [ Razor biblioteca de clases](xref:razor-pages/ui-class) para incluir vistas en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="706b5-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="706b5-182">Impedimento de la carga de los recursos</span><span class="sxs-lookup"><span data-stu-id="706b5-182">Prevent loading resources</span></span>

<span data-ttu-id="706b5-183">Los elementos de aplicación se pueden usar para *evitar* la carga de recursos en un ensamblado o ubicación en concreto.</span><span class="sxs-lookup"><span data-stu-id="706b5-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="706b5-184">Agregue o quite miembros de la colección <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar los recursos o hacer que estos estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="706b5-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="706b5-185">El orden de las entradas de la colección `ApplicationParts` es irrelevante.</span><span class="sxs-lookup"><span data-stu-id="706b5-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="706b5-186">Configure `ApplicationPartManager` antes de usarlo para configurar los servicios en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="706b5-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="706b5-187">Por ejemplo, configure `ApplicationPartManager` antes de invocar a `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="706b5-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="706b5-188">Llame a `Remove` en la colección `ApplicationParts` para quitar un recurso.</span><span class="sxs-lookup"><span data-stu-id="706b5-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="706b5-189">En el código siguiente se usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para quitar `MyDependentLibrary` de la aplicación: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="706b5-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="706b5-190">`ApplicationPartManager` incluye elementos para:</span><span class="sxs-lookup"><span data-stu-id="706b5-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="706b5-191">El ensamblado de la aplicación y los ensamblados dependientes.</span><span class="sxs-lookup"><span data-stu-id="706b5-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="706b5-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="706b5-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="706b5-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="706b5-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="706b5-194">Proveedores de características de la aplicación</span><span class="sxs-lookup"><span data-stu-id="706b5-194">Application feature providers</span></span>

<span data-ttu-id="706b5-195">Los proveedores de características de la aplicación examinan los elementos de aplicación y proporcionan características para esos elementos.</span><span class="sxs-lookup"><span data-stu-id="706b5-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="706b5-196">Existen proveedores de características integrados para las siguientes características de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="706b5-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="706b5-197">Controladores</span><span class="sxs-lookup"><span data-stu-id="706b5-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="706b5-198">Aplicaciones auxiliares de etiquetas</span><span class="sxs-lookup"><span data-stu-id="706b5-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="706b5-199">Ver componentes</span><span class="sxs-lookup"><span data-stu-id="706b5-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="706b5-200">Los proveedores de características heredan de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, donde `T` es el tipo de la característica.</span><span class="sxs-lookup"><span data-stu-id="706b5-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="706b5-201">Los proveedores de características se pueden implementar para cualquiera de los tipos de características enumerados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="706b5-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="706b5-202">El orden de los proveedores de características en `ApplicationPartManager.FeatureProviders` puede afectar al comportamiento en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="706b5-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="706b5-203">Los proveedores agregados posteriormente pueden reaccionar ante las acciones realizadas por los proveedores agregados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="706b5-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="706b5-204">muestra de las características disponibles</span><span class="sxs-lookup"><span data-stu-id="706b5-204">Display available features</span></span>

<span data-ttu-id="706b5-205">Las características disponibles para una aplicación se pueden enumerar solicitando `ApplicationPartManager` a través de la [inserción de dependencias](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="706b5-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="706b5-206">El [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) usa el código anterior para mostrar las características de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="706b5-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="706b5-207">Detección en elementos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="706b5-207">Discovery in application parts</span></span>

<span data-ttu-id="706b5-208">Los errores HTTP 404 no son frecuentes cuando se usan elementos de aplicaciones para el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="706b5-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="706b5-209">Normalmente, estos errores se producen porque falta un requisito esencial sobre cómo se detectan los elementos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="706b5-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="706b5-210">Si la aplicación devuelve un error HTTP 404, verifique que se cumplen los siguientes requisitos:</span><span class="sxs-lookup"><span data-stu-id="706b5-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="706b5-211">La configuración `applicationName` debe establecerse en el ensamblado raíz que se usa para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="706b5-212">El ensamblado raíz que se usa para la detección suele ser el ensamblado del punto de entrada.</span><span class="sxs-lookup"><span data-stu-id="706b5-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="706b5-213">El ensamblado raíz debe tener una referencia a las partes que se usan para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="706b5-214">La referencia puede ser directa o transitiva.</span><span class="sxs-lookup"><span data-stu-id="706b5-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="706b5-215">El ensamblado raíz debe hacer referencia al SDK web.</span><span class="sxs-lookup"><span data-stu-id="706b5-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="706b5-216">El marco de ASP.NET Core tiene una lógica de compilación personalizada que marca atributos en el ensamblado raíz que se usan para la detección.</span><span class="sxs-lookup"><span data-stu-id="706b5-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
