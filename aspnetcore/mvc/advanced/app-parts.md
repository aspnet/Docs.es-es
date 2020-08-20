---
title: Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación en ASP.net Core
author: rick-anderson
description: Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación en ASP.net Core
ms.author: riande
ms.date: 11/11/2019
no-loc:
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
ms.openlocfilehash: 2e86025eaf98c4e2cbbd86a5a353664204c35594
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630424"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>Comparta controladores, vistas, Razor páginas, etc. con los elementos de la aplicación

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Un *elemento de aplicación* es una abstracción sobre los recursos de una aplicación. Los elementos de aplicación permiten a ASP.NET Core detectar controladores, ver componentes, aplicaciones auxiliares de etiquetas, Razor páginas, orígenes de compilación de Razor, etc. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> es un elemento de una aplicación. `AssemblyPart` encapsula una referencia de ensamblado y expone los tipos y las referencias de compilación.

Los [proveedores de características](#fp) trabajan con los elementos de aplicación para rellenar las características de una aplicación de ASP.NET Core. El caso de uso principal de los elementos de aplicación es configurar una aplicación para detectar (o evitar cargar) características de ASP.NET Core de un ensamblado. Por ejemplo, puede que desee compartir la funcionalidad común entre varias aplicaciones. Mediante el uso de elementos de aplicación, puede compartir un ensamblado (DLL) que contiene controladores, vistas, Razor páginas, orígenes de compilación de Razor, aplicaciones auxiliares de etiquetas y mucho más con varias aplicaciones. Es preferible compartir un ensamblado a duplicar el código en varios proyectos.

Las aplicaciones ASP.NET Core cargan características de <xref:System.Web.WebPages.ApplicationPart>. La clase <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa un elemento de aplicación que está respaldado por un ensamblado.

## <a name="load-aspnet-core-features"></a>Carga de características de ASP.NET Core

Use las clases <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> y <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> para detectar y cargar características de ASP.NET Core (controladores, componentes de vista, etc.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> realiza un seguimiento de los elementos de aplicación y los proveedores de características disponibles. `ApplicationPartManager` se configura en `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

El código siguiente proporciona un enfoque alternativo para configurar `ApplicationPartManager` mediante `AssemblyPart`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Los dos ejemplos de código anteriores cargan `SharedController` de un ensamblado. `SharedController` no se encuentra en el proyecto de la aplicación. Vea la descarga de ejemplo de la [solución WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts).

### <a name="include-views"></a>Inclusión de vistas

Use una [ Razor biblioteca de clases](xref:razor-pages/ui-class) para incluir vistas en el ensamblado.

### <a name="prevent-loading-resources"></a>Impedimento de la carga de los recursos

Los elementos de aplicación se pueden usar para *evitar* la carga de recursos en un ensamblado o ubicación en concreto. Agregue o quite miembros de la colección <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar los recursos o hacer que estos estén disponibles. El orden de las entradas de la colección `ApplicationParts` es irrelevante. Configure `ApplicationPartManager` antes de usarlo para configurar los servicios en el contenedor. Por ejemplo, configure `ApplicationPartManager` antes de invocar a `AddControllersAsServices`. Llame a `Remove` en la colección `ApplicationParts` para quitar un recurso.

`ApplicationPartManager` incluye elementos para:

* El ensamblado de la aplicación y los ensamblados dependientes.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Proveedores de características

Los proveedores de características de la aplicación examinan los elementos de aplicación y proporcionan características para esos elementos. Existen proveedores de características integrados para las siguientes características de ASP.NET Core:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Los proveedores de características heredan de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, donde `T` es el tipo de la característica. Los proveedores de características se pueden implementar para cualquiera de los tipos de características enumerados anteriormente. El orden de los proveedores de características en `ApplicationPartManager.FeatureProviders` puede afectar al comportamiento en tiempo de ejecución. Los proveedores agregados posteriormente pueden reaccionar ante las acciones realizadas por los proveedores agregados anteriormente.

### <a name="display-available-features"></a>muestra de las características disponibles

Las características disponibles para una aplicación se pueden enumerar solicitando `ApplicationPartManager` a través de la [inserción de dependencias](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

El [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa el código anterior para mostrar las características de la aplicación:

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

## <a name="discovery-in-application-parts"></a>Detección en elementos de aplicaciones

Los errores HTTP 404 no son frecuentes cuando se usan elementos de aplicaciones para el desarrollo. Normalmente, estos errores se producen porque falta un requisito esencial sobre cómo se detectan los elementos de aplicaciones. Si la aplicación devuelve un error HTTP 404, verifique que se cumplen los siguientes requisitos:

* La configuración `applicationName` debe establecerse en el ensamblado raíz que se usa para la detección. El ensamblado raíz que se usa para la detección suele ser el ensamblado del punto de entrada.
* El ensamblado raíz debe tener una referencia a las partes que se usan para la detección. La referencia puede ser directa o transitiva.
* El ensamblado raíz debe hacer referencia al SDK web. El marco tiene una lógica que marca atributos en el ensamblado raíz que se usan para la detección.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Un *elemento de aplicación* es una abstracción sobre los recursos de una aplicación. Los elementos de aplicación permiten a ASP.NET Core detectar controladores, ver componentes, aplicaciones auxiliares de etiquetas, Razor páginas, orígenes de compilación de Razor, etc. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is un elemento de aplicación. `AssemblyPart` encapsula una referencia de ensamblado y expone los tipos y las referencias de compilación.

Los *proveedores de características* trabajan con los elementos de aplicación para rellenar las características de una aplicación de ASP.NET Core. El caso de uso principal de los elementos de aplicación es configurar una aplicación para detectar (o evitar cargar) características de ASP.NET Core de un ensamblado. Por ejemplo, puede que desee compartir la funcionalidad común entre varias aplicaciones. Mediante el uso de elementos de aplicación, puede compartir un ensamblado (DLL) que contiene controladores, vistas, Razor páginas, orígenes de compilación de Razor, aplicaciones auxiliares de etiquetas y mucho más con varias aplicaciones. Es preferible compartir un ensamblado a duplicar el código en varios proyectos.

Las aplicaciones ASP.NET Core cargan características de <xref:System.Web.WebPages.ApplicationPart>. La clase <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa un elemento de aplicación que está respaldado por un ensamblado.

## <a name="load-aspnet-core-features"></a>Carga de características de ASP.NET Core

Use las clases `ApplicationPart` y `AssemblyPart` para detectar y cargar características de ASP.NET Core (controladores, componentes de vista, etc.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> realiza un seguimiento de los elementos de aplicación y los proveedores de características disponibles. `ApplicationPartManager` se configura en `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

El código siguiente proporciona un enfoque alternativo para configurar `ApplicationPartManager` mediante `AssemblyPart`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Los dos ejemplos de código anteriores cargan `SharedController` de un ensamblado. `SharedController` no está en el proyecto de la aplicación. Vea la descarga de ejemplo de la [solución WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).

### <a name="include-views"></a>Inclusión de vistas

Use una [ Razor biblioteca de clases](xref:razor-pages/ui-class) para incluir vistas en el ensamblado.

### <a name="prevent-loading-resources"></a>Impedimento de la carga de los recursos

Los elementos de aplicación se pueden usar para *evitar* la carga de recursos en un ensamblado o ubicación en concreto. Agregue o quite miembros de la colección <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar los recursos o hacer que estos estén disponibles. El orden de las entradas de la colección `ApplicationParts` es irrelevante. Configure `ApplicationPartManager` antes de usarlo para configurar los servicios en el contenedor. Por ejemplo, configure `ApplicationPartManager` antes de invocar a `AddControllersAsServices`. Llame a `Remove` en la colección `ApplicationParts` para quitar un recurso.

En el código siguiente se usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para quitar `MyDependentLibrary` de la aplicación: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` incluye elementos para:

* El ensamblado de la aplicación y los ensamblados dependientes.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Proveedores de características de la aplicación

Los proveedores de características de la aplicación examinan los elementos de aplicación y proporcionan características para esos elementos. Existen proveedores de características integrados para las siguientes características de ASP.NET Core:

* [Controladores](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Aplicaciones auxiliares de etiquetas](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Ver componentes](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Los proveedores de características heredan de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, donde `T` es el tipo de la característica. Los proveedores de características se pueden implementar para cualquiera de los tipos de características enumerados anteriormente. El orden de los proveedores de características en `ApplicationPartManager.FeatureProviders` puede afectar al comportamiento en tiempo de ejecución. Los proveedores agregados posteriormente pueden reaccionar ante las acciones realizadas por los proveedores agregados anteriormente.

### <a name="display-available-features"></a>muestra de las características disponibles

Las características disponibles para una aplicación se pueden enumerar solicitando `ApplicationPartManager` a través de la [inserción de dependencias](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

El [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa el código anterior para mostrar las características de la aplicación:

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

## <a name="discovery-in-application-parts"></a>Detección en elementos de aplicaciones

Los errores HTTP 404 no son frecuentes cuando se usan elementos de aplicaciones para el desarrollo. Normalmente, estos errores se producen porque falta un requisito esencial sobre cómo se detectan los elementos de aplicaciones. Si la aplicación devuelve un error HTTP 404, verifique que se cumplen los siguientes requisitos:

* La configuración `applicationName` debe establecerse en el ensamblado raíz que se usa para la detección. El ensamblado raíz que se usa para la detección suele ser el ensamblado del punto de entrada.
* El ensamblado raíz debe tener una referencia a las partes que se usan para la detección. La referencia puede ser directa o transitiva.
* El ensamblado raíz debe hacer referencia al SDK web.
  * El marco de ASP.NET Core tiene una lógica de compilación personalizada que marca atributos en el ensamblado raíz que se usan para la detección.

::: moniker-end
