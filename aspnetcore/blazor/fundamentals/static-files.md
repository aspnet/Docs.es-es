---
title: Archivos estáticos Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo configurar y administrar archivos estáticos para aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "100109962"
---
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="9acb6-103">Archivos estáticos Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9acb6-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="9acb6-104">*Este artículo se aplica a Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="9acb6-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="9acb6-105">Para crear asignaciones de archivos adicionales con un objeto <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurar otros valores <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **uno** de los enfoques siguientes.</span><span class="sxs-lookup"><span data-stu-id="9acb6-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="9acb6-106">En los ejemplos siguientes, el marcador de posición `{EXTENSION}` es la extensión de archivo y `{CONTENT TYPE}` es el tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="9acb6-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="9acb6-107">Configure las opciones a través de la [inserción de dependencias (DI)](xref:blazor/fundamentals/dependency-injection) en `Startup.ConfigureServices` (`Startup.cs`) mediante <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span><span class="sxs-lookup"><span data-stu-id="9acb6-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="9acb6-108">Como este enfoque configura el mismo proveedor de archivos que se ha usado para proporcionar `blazor.server.js`, asegúrese de que la configuración personalizada no interfiera con la acción de proporcionar `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="9acb6-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="9acb6-109">Por ejemplo, no quite la asignación para los archivos JavaScript mediante la configuración del proveedor con `provider.Mappings.Remove(".js")`.</span><span class="sxs-lookup"><span data-stu-id="9acb6-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="9acb6-110">Use dos llamadas a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> en `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="9acb6-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="9acb6-111">Configure el proveedor de archivos personalizado en la primera llamada con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="9acb6-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="9acb6-112">El segundo middleware proporciona `blazor.server.js`, que usa la configuración predeterminada de los archivos estáticos proporcionada por el marco Blazor.</span><span class="sxs-lookup"><span data-stu-id="9acb6-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="9acb6-113">Puede evitar interferir con el servicio de `_framework/blazor.server.js` mediante <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> para ejecutar un middleware de archivos estáticos personalizado:</span><span class="sxs-lookup"><span data-stu-id="9acb6-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
