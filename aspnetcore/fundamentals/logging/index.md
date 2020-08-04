---
title: Registros en .NET Core y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la plataforma de registro proporcionada por el paquete NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330770"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="8a7f2-103">Registros en .NET Core y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a7f2-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a7f2-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a7f2-105">.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8a7f2-106">En este artículo se muestra cómo usar las API de registro con proveedores integrados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="8a7f2-107">La mayoría de los ejemplos de código que se muestran en este artículo son de aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="8a7f2-108">Las partes específicas de registro de estos fragmentos de código son válidas para cualquier aplicación de .NET Core que use el [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="8a7f2-109">Las plantillas de aplicación Web de ASP.NET Core usan el host genérico.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="8a7f2-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="8a7f2-111">Proveedores de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-111">Logging providers</span></span>

<span data-ttu-id="8a7f2-112">Los proveedores de registro almacenan los registros, a excepción del proveedor `Console`, que los muestra.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="8a7f2-113">Por ejemplo, el proveedor de Azure Application Insights almacena los registros en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="8a7f2-114">Se pueden habilitar varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="8a7f2-115">Las plantillas de aplicación web de ASP.NET Core predeterminadas:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="8a7f2-116">usan el [host genérico](xref:fundamentals/host/generic-host);</span><span class="sxs-lookup"><span data-stu-id="8a7f2-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="8a7f2-117">llaman a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="8a7f2-118">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-118">Console</span></span>](#console)
  * [<span data-ttu-id="8a7f2-119">Depurar</span><span class="sxs-lookup"><span data-stu-id="8a7f2-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="8a7f2-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="8a7f2-121">[EventLog](#welog): Solo Windows</span><span class="sxs-lookup"><span data-stu-id="8a7f2-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="8a7f2-122">En el código anterior se muestra la clase `Program` creada con las plantillas de aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="8a7f2-123">En las siguientes secciones se proporcionan ejemplos basados en las plantillas de aplicación web de ASP.NET Core, que usan el host genérico.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="8a7f2-124">Las [aplicaciones de consola que no son de host](#nhca) se explican más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="8a7f2-125">Para invalidar el conjunto predeterminado de proveedores de registro agregados por `Host.CreateDefaultBuilder`, llame a `ClearProviders` y agregue los proveedores de registro necesarios.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="8a7f2-126">Por ejemplo, el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-126">For example, the following code:</span></span>

* <span data-ttu-id="8a7f2-127">Llama a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> para quitar todas las instancias de <xref:Microsoft.Extensions.Logging.ILoggerProvider> del generador.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="8a7f2-128">Agrega el proveedor de registro [Console](#console).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="8a7f2-129">Para otros proveedores, vea:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-129">For additional providers, see:</span></span>

* [<span data-ttu-id="8a7f2-130">Proveedores de registro integrados</span><span class="sxs-lookup"><span data-stu-id="8a7f2-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="8a7f2-131">[Proveedores de registro de terceros](#third-party-logging-providers)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="8a7f2-132">Creación de registros</span><span class="sxs-lookup"><span data-stu-id="8a7f2-132">Create logs</span></span>

<span data-ttu-id="8a7f2-133">Para crear registros, use un objeto <xref:Microsoft.Extensions.Logging.ILogger%601> desde la [inserción de dependencias](xref:fundamentals/dependency-injection) (DI):</span><span class="sxs-lookup"><span data-stu-id="8a7f2-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8a7f2-134">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-134">The following example:</span></span>

* <span data-ttu-id="8a7f2-135">Crea un registrador, `ILogger<AboutModel>`, que utiliza una *categoría* de registro del nombre completo del tipo `AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="8a7f2-136">La categoría de registro es una cadena que está asociada con cada registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="8a7f2-137">Llama a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> para realizar el registro en el nivel de `Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="8a7f2-138">El *nivel* de registro indica la gravedad del evento registrado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="8a7f2-139">Los [niveles](#log-level) y las [categorías](#log-category) se explican con más detalle posteriormente en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="8a7f2-140">Para obtener información sobre Blazor, consulte [Creación de registros en Blazor y Blazor WebAssembly](#clib) en este documento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="8a7f2-141">[Creación de registros en Main y Startup](#clms) muestra cómo crear registros en `Main` y `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="8a7f2-142">registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-142">Configure logging</span></span>

<span data-ttu-id="8a7f2-143">La configuración de registros suele proporcionarla la sección `Logging` de los archivos *appsettings*.`{Environment}` *.json*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="8a7f2-144">El siguiente archivo *appsettings.Development.json* se genera mediante las plantillas de aplicación web de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="8a7f2-145">En el código JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-145">In the preceding JSON:</span></span>

* <span data-ttu-id="8a7f2-146">Se especifican las categorías `"Default"`, `"Microsoft"` y `"Microsoft.Hosting.Lifetime"`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="8a7f2-147">La categoría `"Microsoft"` se aplica a todas las categorías que comienzan por `"Microsoft"`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="8a7f2-148">Por ejemplo, esta configuración se aplica a la categoría `"Microsoft.AspNetCore.Routing.EndpointMiddleware"`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="8a7f2-149">La categoría `"Microsoft"` registra en el nivel de registro `Warning` y superiores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="8a7f2-150">La categoría `"Microsoft.Hosting.Lifetime"` es más específica que la categoría `"Microsoft"`, por lo que la categoría `"Microsoft.Hosting.Lifetime"` registra en el nivel de registro "Information" y superiores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="8a7f2-151">No se especifica un proveedor de registro específico, por lo que `LogLevel` se aplica a todos los proveedores de registro habilitados, excepto [Windows EventLog](#welog).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="8a7f2-152">La propiedad `Logging` puede tener <xref:Microsoft.Extensions.Logging.LogLevel> y registrar propiedades del proveedor de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="8a7f2-153">`LogLevel` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="8a7f2-154">En el código JSON anterior, se especifican los niveles de registro `Information` y `Warning`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="8a7f2-155">`LogLevel` indica la gravedad del registro y los valores están entre 0 y 6:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="8a7f2-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 y `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="8a7f2-157">Cuando se especifica `LogLevel`, el registro está habilitado para los mensajes tanto en el nivel especificado como en los superiores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="8a7f2-158">En el código JSON anterior, se registra la categoría `Default` para `Information` y los niveles posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="8a7f2-159">Por ejemplo, se registran los mensajes `Information`, `Warning`, `Error` y `Critical`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="8a7f2-160">Si no se especifica `LogLevel`, el nivel predeterminado del registro es `Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="8a7f2-161">Para obtener más información, consulte [Niveles de registro](#llvl).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="8a7f2-162">Una propiedad de proveedor puede especificar una propiedad de `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="8a7f2-163">`LogLevel` en un proveedor especifica los niveles que se van a registrar para ese proveedor, e invalida la configuración de registro que no es de proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="8a7f2-164">Fíjese en el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="8a7f2-165">La configuración de `Logging.{providername}.LogLevel` invalida la configuración de `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="8a7f2-166">En el código JSON anterior, el nivel de registro predeterminado del proveedor `Debug` se establece en `Information`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="8a7f2-167">La configuración anterior especifica el nivel de registro `Information` para cada categoría de `Logging:Debug:`, excepto `Microsoft.Hosting`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="8a7f2-168">Cuando se muestra una categoría específica, esa categoría invalida la categoría predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="8a7f2-169">En el JSON anterior, las categorías de `Logging:Debug:LogLevel` `"Microsoft.Hosting"` y `"Default"` invalidan la configuración de `Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="8a7f2-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="8a7f2-170">Se puede especificar el nivel de registro mínimo para:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="8a7f2-171">Proveedores específicos:  Por ejemplo, `Logging:EventSource:LogLevel:Default:Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="8a7f2-172">Categorías específicas: Por ejemplo, `Logging:LogLevel:Microsoft:Warning`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="8a7f2-173">Todos los proveedores y todas las categorías: `Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="8a7f2-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="8a7f2-174">Los registros situados por debajo del nivel mínimo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="8a7f2-175">no se pasan proveedor;</span><span class="sxs-lookup"><span data-stu-id="8a7f2-175">Passed to the provider.</span></span>
* <span data-ttu-id="8a7f2-176">no se registran ni se muestran.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-176">Logged or displayed.</span></span>

<span data-ttu-id="8a7f2-177">Para suprimir todos los registros, especifique [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="8a7f2-178">`LogLevel.None` tiene un valor de 6, que es mayor que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="8a7f2-179">Si un proveedor admite [ámbitos de registro](#logscopes), `IncludeScopes` indica si están habilitados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="8a7f2-180">Para obtener más información, consulte [Ámbitos de registro](#logscopes).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="8a7f2-181">El siguiente archivo *appSettings.json* contiene todos los proveedores habilitados de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="8a7f2-182">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-182">In the preceding sample:</span></span>

* <span data-ttu-id="8a7f2-183">Las categorías y los niveles no son valores sugeridos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="8a7f2-184">El objetivo del ejemplo es mostrar todos los proveedores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="8a7f2-185">La configuración de `Logging.{providername}.LogLevel` invalida la configuración de `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="8a7f2-186">Por ejemplo, el nivel de `Debug.LogLevel.Default` invalida el nivel de `LogLevel.Default`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="8a7f2-187">Se usa cada *alias* de proveedor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="8a7f2-188">Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="8a7f2-189">Los alias de proveedores integrados son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="8a7f2-190">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-190">Console</span></span>
  * <span data-ttu-id="8a7f2-191">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-191">Debug</span></span>
  * <span data-ttu-id="8a7f2-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-192">EventSource</span></span>
  * <span data-ttu-id="8a7f2-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="8a7f2-193">EventLog</span></span>
  * <span data-ttu-id="8a7f2-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="8a7f2-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="8a7f2-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="8a7f2-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="8a7f2-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="8a7f2-197">Establecimiento del nivel de registro mediante la línea de comandos, las variables de entorno y otra configuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="8a7f2-198">El nivel de registro se puede establecer con cualquiera de los [proveedores de configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8a7f2-199">Los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-199">The following commands:</span></span>

* <span data-ttu-id="8a7f2-200">establecen la clave de entorno `Logging:LogLevel:Microsoft` en un valor de `Information` en Windows;</span><span class="sxs-lookup"><span data-stu-id="8a7f2-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="8a7f2-201">prueban la configuración cuando use una aplicación creada con las plantillas de aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="8a7f2-202">El comando `dotnet run` debe ejecutarse en el directorio del proyecto después de usar `set`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="8a7f2-203">La configuración del entorno anterior:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-203">The preceding environment setting:</span></span>

* <span data-ttu-id="8a7f2-204">solo se establece en procesos iniciados desde la ventana de comandos en la que se establecieron;</span><span class="sxs-lookup"><span data-stu-id="8a7f2-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8a7f2-205">no podrá ser leída por los exploradores que se inician con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8a7f2-206">El siguiente comando [setx](/windows-server/administration/windows-commands/setx) también establece la clave y el valor de entorno en Windows.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="8a7f2-207">A diferencia de `set`, la configuración de `setx` se conserva.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8a7f2-208">El modificador `/M` establece la variable en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="8a7f2-209">Si no se usa `/M`, se establece una variable de entorno de usuario.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="8a7f2-210">En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8a7f2-211">Los ajustes de configuración de Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8a7f2-212">Se cifran en reposo y se transmiten a través de un canal cifrado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8a7f2-213">Se exponen como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-213">Exposed as environment variables.</span></span>

<span data-ttu-id="8a7f2-214">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8a7f2-215">Para obtener más información sobre cómo establecer los valores de configuración de ASP.NET Core mediante variables de entorno, vea [Variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="8a7f2-216">Para obtener información sobre el uso de otros orígenes de configuración, como la línea de comandos, Azure Key Vault, Azure App Configuration, otros formatos de archivo, etc., vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="8a7f2-217">Cómo se aplican las reglas de filtro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-217">How filtering rules are applied</span></span>

<span data-ttu-id="8a7f2-218">Cuando se crea un objeto <xref:Microsoft.Extensions.Logging.ILogger%601>, el objeto <xref:Microsoft.Extensions.Logging.ILoggerFactory> selecciona una sola regla por proveedor para aplicar a ese registrador.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="8a7f2-219">Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="8a7f2-220">De las reglas disponibles, se selecciona la más específica para cada par de categoría y proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="8a7f2-221">Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="8a7f2-222">Se seleccionan todas las reglas que coinciden con el proveedor o su alias.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="8a7f2-223">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="8a7f2-224">Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="8a7f2-225">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="8a7f2-226">Si se seleccionan varias reglas, se toma la **última**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="8a7f2-227">Si no se selecciona ninguna regla, se usa `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="8a7f2-228">Registro de la salida de dotnet run y Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a7f2-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="8a7f2-229">Se muestran los registros creados con los [proveedores de registro predeterminados](#lp):</span><span class="sxs-lookup"><span data-stu-id="8a7f2-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="8a7f2-230">En Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a7f2-230">In Visual Studio</span></span>
  * <span data-ttu-id="8a7f2-231">En la ventana de salida de Debug al depurar.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="8a7f2-232">En la ventana de ASP.NET Core Web Server.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="8a7f2-233">En la ventana de la consola cuando la aplicación se ejecuta con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="8a7f2-234">Los registros que comienzan por categorías de "Microsoft" pertenecen al código de marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="8a7f2-235">ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="8a7f2-236">Categoría de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-236">Log category</span></span>

<span data-ttu-id="8a7f2-237">Cuando se crea un objeto `ILogger`, se especifica una *categoría*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="8a7f2-238">Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="8a7f2-239">La cadena de categoría es arbitraria, pero la convención es usar el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="8a7f2-240">Por ejemplo, en un controlador, el nombre podría ser `"TodoApi.Controllers.TodoController"`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="8a7f2-241">Las aplicaciones web de ASP.NET Core usan `ILogger<T>` para obtener automáticamente una instancia de `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="8a7f2-242">Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="8a7f2-243">`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="8a7f2-244">Nivel de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-244">Log level</span></span>

<span data-ttu-id="8a7f2-245">En la tabla siguiente se enumeran los valores de <xref:Microsoft.Extensions.Logging.LogLevel>, el método de extensión `Log{LogLevel}` oportuno y el uso sugerido:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="8a7f2-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8a7f2-246">LogLevel</span></span> | <span data-ttu-id="8a7f2-247">Valor</span><span class="sxs-lookup"><span data-stu-id="8a7f2-247">Value</span></span> | <span data-ttu-id="8a7f2-248">Método</span><span class="sxs-lookup"><span data-stu-id="8a7f2-248">Method</span></span> | <span data-ttu-id="8a7f2-249">Descripción</span><span class="sxs-lookup"><span data-stu-id="8a7f2-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="8a7f2-250">Seguimiento</span><span class="sxs-lookup"><span data-stu-id="8a7f2-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-251">0</span><span class="sxs-lookup"><span data-stu-id="8a7f2-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="8a7f2-252">Contienen los mensajes más detallados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-252">Contain the most detailed messages.</span></span> <span data-ttu-id="8a7f2-253">Estos mensajes pueden contener datos confidenciales de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="8a7f2-254">Están deshabilitados de forma predeterminada y ***no*** se deben habilitar en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="8a7f2-255">Depurar</span><span class="sxs-lookup"><span data-stu-id="8a7f2-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-256">1</span><span class="sxs-lookup"><span data-stu-id="8a7f2-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="8a7f2-257">Para depuración y desarrollo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-257">For debugging and development.</span></span> <span data-ttu-id="8a7f2-258">Debido al elevado volumen, tenga precaución cuando lo use en producción.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="8a7f2-259">Información</span><span class="sxs-lookup"><span data-stu-id="8a7f2-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-260">2</span><span class="sxs-lookup"><span data-stu-id="8a7f2-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="8a7f2-261">Realiza el seguimiento del flujo general de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="8a7f2-262">Puede tener un valor a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-262">May have long-term value.</span></span> |
| [<span data-ttu-id="8a7f2-263">Advertencia</span><span class="sxs-lookup"><span data-stu-id="8a7f2-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-264">3</span><span class="sxs-lookup"><span data-stu-id="8a7f2-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="8a7f2-265">Para eventos anómalos o inesperados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="8a7f2-266">Normalmente incluye errores o estados que no provocan un error en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="8a7f2-267">Error</span><span class="sxs-lookup"><span data-stu-id="8a7f2-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-268">4</span><span class="sxs-lookup"><span data-stu-id="8a7f2-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="8a7f2-269">Para los errores y excepciones que no se pueden controlar.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="8a7f2-270">Estos mensajes indican un error en la operación o solicitud actual, no un error de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| <span data-ttu-id="8a7f2-271">[Critical)](xref:Microsoft.Extensions.Logging.LogLevel) (Crítico)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-271">[Critical](xref:Microsoft.Extensions.Logging.LogLevel)</span></span> | <span data-ttu-id="8a7f2-272">5</span><span class="sxs-lookup"><span data-stu-id="8a7f2-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="8a7f2-273">Para los errores que requieren atención inmediata.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-273">For failures that require immediate attention.</span></span> <span data-ttu-id="8a7f2-274">Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="8a7f2-275">Ninguno</span><span class="sxs-lookup"><span data-stu-id="8a7f2-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="8a7f2-276">6</span><span class="sxs-lookup"><span data-stu-id="8a7f2-276">6</span></span> | | <span data-ttu-id="8a7f2-277">Especifica que una categoría de registro no debe escribir ningún mensaje.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="8a7f2-278">En la tabla anterior, `LogLevel` aparece de menor a mayor gravedad.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="8a7f2-279">El primer parámetro del método [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions), <xref:Microsoft.Extensions.Logging.LogLevel>, indica la gravedad del registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="8a7f2-280">En lugar de llamar a `Log(LogLevel, ...)`, la mayoría de los desarrolladores llaman a los métodos de extensión [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="8a7f2-281">Los métodos de extensión `Log{LogLevel}` [llaman al método Log y especifican el LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="8a7f2-282">Por ejemplo, las dos llamadas de registro siguientes son funcionalmente equivalentes y generan el mismo registro:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="8a7f2-283">`MyLogEvents.TestItem` es el identificador del evento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="8a7f2-284">`MyLogEvents` forma parte de la aplicación de ejemplo y se muestra en la sección [Log event ID](#leid) (id. de evento de registro).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="8a7f2-285">El siguiente código crea los registros `Information` y `Warning`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="8a7f2-286">En el código anterior, el primer parámetro de `Log{LogLevel}`, `MyLogEvents.GetItem`, es el [identificador de evento de registro](#leid).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="8a7f2-287">El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="8a7f2-288">Los parámetros de método se explican detalladamente en la sección [Plantilla de mensaje](#lmt) más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="8a7f2-289">Llame al método `Log{LogLevel}` apropiado para controlar la cantidad de salida de registro que se escribe en un determinado medio de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="8a7f2-290">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-290">For example:</span></span>

* <span data-ttu-id="8a7f2-291">En producción:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-291">In production:</span></span>
  * <span data-ttu-id="8a7f2-292">El registro en los niveles `Trace` o `Information` genera un gran volumen de mensajes de registro detallados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="8a7f2-293">Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="8a7f2-294">Considere la posibilidad de limitar `Trace` y `Information` a categorías específicas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="8a7f2-295">El registro entre los niveles `Warning` y `Critical` debe generar pocos mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="8a7f2-296">Los costos y los límites de almacenamiento no suelen ser un problema.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="8a7f2-297">Cuantos menos registros haya, mayor será la flexibilidad a la hora de elegir el almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="8a7f2-298">En desarrollo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-298">In development:</span></span>
  * <span data-ttu-id="8a7f2-299">Establézcalo en `Warning`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="8a7f2-300">Agregue los mensajes `Trace` o`Information` al solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="8a7f2-301">Para limitar la salida, establezca `Trace` o `Information` solo para las categorías que se están investigando.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="8a7f2-302">ASP.NET Core escribe registros de eventos de marco.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="8a7f2-303">Por ejemplo, tomemos la salida del registro de:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="8a7f2-304">Una aplicación de Razor Pages creada con las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="8a7f2-305">Registro establecido en `Logging:Console:LogLevel:Microsoft:Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="8a7f2-306">Navegación a la página Privacy (privacidad):</span><span class="sxs-lookup"><span data-stu-id="8a7f2-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="8a7f2-307">El siguiente JSON establece `Logging:Console:LogLevel:Microsoft:Information`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="8a7f2-308">Id. de evento del registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-308">Log event ID</span></span>

<span data-ttu-id="8a7f2-309">Cada registro se puede especificar un *id. de evento*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="8a7f2-310">La aplicación de ejemplo usa la clase `MyLogEvents` para definir los identificadores de evento:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="8a7f2-311">Un id. de evento asocia un conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-311">An event ID associates a set of events.</span></span> <span data-ttu-id="8a7f2-312">Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="8a7f2-313">El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="8a7f2-314">El proveedor de depuración no muestra los identificadores de evento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="8a7f2-315">El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="8a7f2-316">Algunos proveedores de registro almacenan el identificador de evento en un campo, lo que permite filtrar por el id.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="8a7f2-317">Plantilla de mensaje de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="8a7f2-318">Cada API de registro usa una plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-318">Each log API uses a message template.</span></span> <span data-ttu-id="8a7f2-319">La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="8a7f2-320">Use los nombres de los marcadores de posición, no números.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="8a7f2-321">El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="8a7f2-322">En el código siguiente, los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="8a7f2-323">El código anterior crea un mensaje de registro con los valores de parámetro en secuencia:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="8a7f2-324">Este enfoque permite a los proveedores de registro implementar [registro semántico o estructurado](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="8a7f2-325">Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="8a7f2-326">Esto permite a los proveedores de registro almacenar los valores de parámetro como campos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="8a7f2-327">Por ejemplo, tomemos el siguiente método de registrador:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="8a7f2-328">Por ejemplo, al registrar en Azure Table Storage:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="8a7f2-329">Cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="8a7f2-330">Las tablas con propiedades simplifican las consultas en los datos registrados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="8a7f2-331">Por ejemplo, una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin necesidad de analizar el tiempo de espera del mensaje de texto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="8a7f2-332">Registro de excepciones</span><span class="sxs-lookup"><span data-stu-id="8a7f2-332">Log exceptions</span></span>

<span data-ttu-id="8a7f2-333">Los métodos de registrador tienen sobrecargas que toman un parámetro de excepción:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="8a7f2-334">El registro de excepciones es específico del proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="8a7f2-335">Nivel de registro predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-335">Default log level</span></span>

<span data-ttu-id="8a7f2-336">Si no se establece el nivel de registro predeterminado, su valor será `Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="8a7f2-337">Por ejemplo, considere la siguiente aplicación web:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="8a7f2-338">Creada con las plantillas de aplicación web de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="8a7f2-339">*appsettings.json* y *appsettings.Development.json* eliminados o con el nombre cambiado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="8a7f2-340">Con la configuración anterior, al navegar a la página de privacidad o de inicio, se generan muchos mensajes de `Trace`, `Debug` y `Information` con `Microsoft` en el nombre de la categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="8a7f2-341">El código siguiente establece el nivel de registro predeterminado cuando este no se establece en la configuración:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="8a7f2-342">En general, los niveles de registro se deben especificar en la configuración y no en el código.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="8a7f2-343">Función de filtro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-343">Filter function</span></span>

<span data-ttu-id="8a7f2-344">Se invoca una función de filtro para todos los proveedores y las categorías que no tienen reglas asignadas mediante configuración o código:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="8a7f2-345">El código anterior muestra los registros de la consola cuando la categoría contiene `Controller` o `Microsoft` y el nivel de registro es `Information` o superior.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="8a7f2-346">En general, los niveles de registro se deben especificar en la configuración y no en el código.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="8a7f2-347">Categorías ASP.NET Core y EF Core</span><span class="sxs-lookup"><span data-stu-id="8a7f2-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="8a7f2-348">La tabla siguiente conti9ene algunas categorías usadas por ASP.NET Core y Entity Framework Core, con notas sobre los registros:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="8a7f2-349">Categoría</span><span class="sxs-lookup"><span data-stu-id="8a7f2-349">Category</span></span>                            | <span data-ttu-id="8a7f2-350">Notas</span><span class="sxs-lookup"><span data-stu-id="8a7f2-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="8a7f2-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="8a7f2-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="8a7f2-352">Diagnósticos generales de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="8a7f2-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="8a7f2-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="8a7f2-354">Qué claves se tuvieron en cuenta, encontraron y usaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="8a7f2-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="8a7f2-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="8a7f2-356">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-356">Hosts allowed.</span></span> |
| <span data-ttu-id="8a7f2-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="8a7f2-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="8a7f2-358">Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="8a7f2-359">Qué ensamblados de inicio de hospedaje se cargaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="8a7f2-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="8a7f2-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="8a7f2-361">Diagnósticos de MVC y Razor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="8a7f2-362">Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="8a7f2-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="8a7f2-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="8a7f2-364">Información de coincidencia de ruta.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-364">Route matching information.</span></span> |
| <span data-ttu-id="8a7f2-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="8a7f2-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="8a7f2-366">Inicio y detención de conexión y mantener las respuestas activas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="8a7f2-367">Información de certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="8a7f2-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="8a7f2-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="8a7f2-369">Archivos servidos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-369">Files served.</span></span> |
| <span data-ttu-id="8a7f2-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8a7f2-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="8a7f2-371">Diagnósticos generales de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="8a7f2-372">Actividad y la configuración de bases de datos, detección de cambios y migraciones.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="8a7f2-373">Para ver más categorías en la ventana de la consola, establezca **appsettings.Development.json** en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="8a7f2-374">Ámbitos de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-374">Log scopes</span></span>

 <span data-ttu-id="8a7f2-375">Un *ámbito* puede agrupar un conjunto de operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="8a7f2-376">Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="8a7f2-377">Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="8a7f2-378">Un ámbito:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-378">A scope:</span></span>

* <span data-ttu-id="8a7f2-379">es un tipo <xref:System.IDisposable> devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>;</span><span class="sxs-lookup"><span data-stu-id="8a7f2-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="8a7f2-380">se mantiene hasta que se elimina.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="8a7f2-381">Los siguientes proveedores admiten ámbitos:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="8a7f2-382">AzureAppServicesFile y AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="8a7f2-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="8a7f2-383">Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="8a7f2-384">El JSON siguiente habilita ámbitos para el proveedor de la consola:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="8a7f2-385">El código siguiente permite ámbitos para el proveedor de la consola:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="8a7f2-386">En general, el registro se debe especificar en la configuración y no en el código.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="8a7f2-387">Proveedores de registro integrados</span><span class="sxs-lookup"><span data-stu-id="8a7f2-387">Built-in logging providers</span></span>

<span data-ttu-id="8a7f2-388">ASP.NET Core incluye los siguientes proveedores de registro:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="8a7f2-389">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-389">Console</span></span>](#console)
* [<span data-ttu-id="8a7f2-390">Depurar</span><span class="sxs-lookup"><span data-stu-id="8a7f2-390">Debug</span></span>](#debug)
* [<span data-ttu-id="8a7f2-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="8a7f2-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="8a7f2-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="8a7f2-393">AzureAppServicesFile y AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="8a7f2-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="8a7f2-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="8a7f2-395">Para obtener información sobre `stdout` y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="8a7f2-396">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-396">Console</span></span>

<span data-ttu-id="8a7f2-397">El proveedor `Console` registra la salida en la consola.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="8a7f2-398">Para obtener más información sobre cómo ver los registros de `Console` en desarrollo, consulte [Registro de la salida de dotnet run y Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="8a7f2-399">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-399">Debug</span></span>

<span data-ttu-id="8a7f2-400">El proveedor `Debug` escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="8a7f2-401">Las llamadas a `System.Diagnostics.Debug.WriteLine` escriben en el proveedor `Debug`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="8a7f2-402">En Linux, la ubicación del registro del proveedor `Debug` depende de la distribución y puede ser una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="8a7f2-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="8a7f2-403">*/var/log/message*</span></span>
* <span data-ttu-id="8a7f2-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="8a7f2-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="8a7f2-405">Origen de eventos</span><span class="sxs-lookup"><span data-stu-id="8a7f2-405">Event Source</span></span>

<span data-ttu-id="8a7f2-406">El proveedor `EventSource` escribe en un origen de eventos multiplataforma con el nombre `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="8a7f2-407">En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="8a7f2-408">herramienta de seguimiento de dotnet</span><span class="sxs-lookup"><span data-stu-id="8a7f2-408">dotnet trace tooling</span></span>

<span data-ttu-id="8a7f2-409">La herramienta de [seguimiento de dotnet](/dotnet/core/diagnostics/dotnet-trace) es una herramienta global de CLI multiplataforma que permite la recopilación de seguimientos de .NET Core de un proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="8a7f2-410">La herramienta recopila datos del proveedor <xref:Microsoft.Extensions.Logging.EventSource> mediante un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="8a7f2-411">Vea [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) para obtener instrucciones de instalación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="8a7f2-412">Use la herramienta de seguimiento de dotnet para recopilar un seguimiento de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="8a7f2-413">Ejecute la aplicación con el comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="8a7f2-414">Determine el identificador del proceso (PID) de la aplicación .NET Core:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="8a7f2-415">En Windows, siga uno de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="8a7f2-416">Administrador de tareas (Ctrl + Alt + Supr)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="8a7f2-417">Comando TaskList</span><span class="sxs-lookup"><span data-stu-id="8a7f2-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="8a7f2-418">Comando de PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="8a7f2-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="8a7f2-419">En Linux, use el [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="8a7f2-420">Busque el PID del proceso que tenga el mismo nombre que el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="8a7f2-421">Ejecute el comando `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="8a7f2-422">Sintaxis general del comando:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="8a7f2-423">Al usar un shell de comandos de PowerShell, incluya el valor `--providers` entre comillas simples (`'`):</span><span class="sxs-lookup"><span data-stu-id="8a7f2-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="8a7f2-424">En plataformas que no sean Windows, agregue la opción `-f speedscope` para cambiar el formato del archivo de seguimiento de salida a `speedscope`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="8a7f2-425">Palabra clave</span><span class="sxs-lookup"><span data-stu-id="8a7f2-425">Keyword</span></span> | <span data-ttu-id="8a7f2-426">Descripción</span><span class="sxs-lookup"><span data-stu-id="8a7f2-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="8a7f2-427">1</span><span class="sxs-lookup"><span data-stu-id="8a7f2-427">1</span></span>       | <span data-ttu-id="8a7f2-428">Registre los eventos meta sobre el elemento `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="8a7f2-429">No registre eventos de `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="8a7f2-430">2</span><span class="sxs-lookup"><span data-stu-id="8a7f2-430">2</span></span>       | <span data-ttu-id="8a7f2-431">Activa el evento `Message` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="8a7f2-432">Proporciona la información mediante programación (sin formato).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="8a7f2-433">4</span><span class="sxs-lookup"><span data-stu-id="8a7f2-433">4</span></span>       | <span data-ttu-id="8a7f2-434">Activa el evento `FormatMessage` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="8a7f2-435">Proporciona la versión de cadena con formato de la información.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="8a7f2-436">8</span><span class="sxs-lookup"><span data-stu-id="8a7f2-436">8</span></span>       | <span data-ttu-id="8a7f2-437">Activa el evento `MessageJson` cuando se llama a `ILogger.Log()`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="8a7f2-438">Proporciona una representación JSON de los argumentos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="8a7f2-439">Nivel de evento</span><span class="sxs-lookup"><span data-stu-id="8a7f2-439">Event Level</span></span> | <span data-ttu-id="8a7f2-440">Descripción</span><span class="sxs-lookup"><span data-stu-id="8a7f2-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="8a7f2-441">0</span><span class="sxs-lookup"><span data-stu-id="8a7f2-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="8a7f2-442">1</span><span class="sxs-lookup"><span data-stu-id="8a7f2-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="8a7f2-443">2</span><span class="sxs-lookup"><span data-stu-id="8a7f2-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="8a7f2-444">3</span><span class="sxs-lookup"><span data-stu-id="8a7f2-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="8a7f2-445">4</span><span class="sxs-lookup"><span data-stu-id="8a7f2-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="8a7f2-446">5</span><span class="sxs-lookup"><span data-stu-id="8a7f2-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="8a7f2-447">Las entradas `FilterSpecs` de `{Logger Category}` y `{Event Level}` representan condiciones de filtrado de registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="8a7f2-448">Separe las entradas `FilterSpecs` con un punto y coma (`;`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="8a7f2-449">Ejemplo de uso de un shell de comandos de Windows (**no** hay comillas simples alrededor del valor `--providers`):</span><span class="sxs-lookup"><span data-stu-id="8a7f2-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="8a7f2-450">El comando anterior activa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-450">The preceding command activates:</span></span>

   * <span data-ttu-id="8a7f2-451">Registrador de origen del evento para generar cadenas con formato (`4`) de los errores (`2`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="8a7f2-452">Registro `Microsoft.AspNetCore.Hosting` en el nivel de registro `Informational` (`4`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="8a7f2-453">Presione la tecla Entrar o Ctrl + C para detener las herramientas de seguimiento de dotnet.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="8a7f2-454">El seguimiento se guarda con el nombre *trace.nettrace* en la carpeta en la que se ejecuta el comando `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="8a7f2-455">Abra el seguimiento con [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="8a7f2-456">Abra el archivo *trace.nettrace* y explore los eventos de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="8a7f2-457">Si la aplicación no compila el host con `CreateDefaultBuilder`, agregue el [proveedor de origen del evento](#event-source-provider) a la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="8a7f2-458">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-458">For more information, see:</span></span>

* <span data-ttu-id="8a7f2-459">[Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentación de .NET Core)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="8a7f2-460">[Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentación del repositorio de GitHub sobre diagnóstico y dotnet)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="8a7f2-461">[Clase LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorador de API de .NET)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="8a7f2-462">[Origen de referencia de LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Para obtener el origen de referencia de otra versión, cambie la rama a `release/{Version}`, donde `{Version}` corresponde a la versión de ASP.NET Core deseada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="8a7f2-463">[Perfview](#perfview): es útil para ver los seguimientos del origen de eventos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="8a7f2-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="8a7f2-464">Perfview</span></span>

<span data-ttu-id="8a7f2-465">Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="8a7f2-466">Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="8a7f2-467">Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="8a7f2-468">No olvide el símbolo `*` al principio de la cadena.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="8a7f2-469">Registro de eventos de Windows</span><span class="sxs-lookup"><span data-stu-id="8a7f2-469">Windows EventLog</span></span>

<span data-ttu-id="8a7f2-470">El proveedor `EventLog` envía la salida del registro al Registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="8a7f2-471">A diferencia de otros proveedores, el proveedor `EventLog` ***no*** hereda la configuración de no proveedor predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="8a7f2-472">Si no se especifican valores de registro de `EventLog`, [el valor predeterminado será LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="8a7f2-473">Para registrar eventos inferiores a <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, establezca el nivel de registro de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="8a7f2-474">En el ejemplo siguiente se establece el nivel de registro predeterminado del registro de eventos en <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="8a7f2-475">Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pueden pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="8a7f2-476">Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="8a7f2-477">`LogName`: "Application"</span><span class="sxs-lookup"><span data-stu-id="8a7f2-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="8a7f2-478">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="8a7f2-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="8a7f2-479">`MachineName`: se usa el nombre del equipo local.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="8a7f2-480">En el código siguiente se cambia el valor predeterminado de `SourceName` (`".NET Runtime"`) por `MyLogs`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="8a7f2-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a7f2-481">Azure App Service</span></span>

<span data-ttu-id="8a7f2-482">El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="8a7f2-483">El paquete del proveedor no se incluye en el marco compartido.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="8a7f2-484">Para usar el proveedor, agregue el paquete del proveedor al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="8a7f2-485">Para configurar las opciones de proveedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> y <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="8a7f2-486">Cuando se implementa en Azure App Service, la aplicación usa la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) de la página **App Service** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="8a7f2-487">Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="8a7f2-488">**Registro de la aplicación (sistema de archivos)**</span><span class="sxs-lookup"><span data-stu-id="8a7f2-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="8a7f2-489">**Registro de la aplicación (blob)**</span><span class="sxs-lookup"><span data-stu-id="8a7f2-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="8a7f2-490">La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="8a7f2-491">El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="8a7f2-492">El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="8a7f2-493">El proveedor solo realiza registros cuando el proyecto se ejecuta en el entorno de Azure.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="8a7f2-494">Secuencias de registro de Azure</span><span class="sxs-lookup"><span data-stu-id="8a7f2-494">Azure log streaming</span></span>

<span data-ttu-id="8a7f2-495">El streaming de registro de Azure permiten ver la actividad de registro en tiempo real desde:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="8a7f2-496">El servidor de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="8a7f2-496">The app server</span></span>
* <span data-ttu-id="8a7f2-497">El servidor web</span><span class="sxs-lookup"><span data-stu-id="8a7f2-497">The web server</span></span>
* <span data-ttu-id="8a7f2-498">Error del seguimiento de solicitudes</span><span class="sxs-lookup"><span data-stu-id="8a7f2-498">Failed request tracing</span></span>

<span data-ttu-id="8a7f2-499">Para configurar las secuencias de registro de Azure:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="8a7f2-500">Desplácese a la página **Registros de App Service** desde la página del portal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="8a7f2-501">Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="8a7f2-502">Elija el **Nivel** de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-502">Choose the log **Level**.</span></span> <span data-ttu-id="8a7f2-503">Esta configuración solo se aplica al streaming de registro de Azure.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="8a7f2-504">Desplácese a la página **Secuencia de registro** para ver los registros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="8a7f2-505">Los mensajes que se registran lo hacen con la interfaz `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="8a7f2-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-506">Azure Application Insights</span></span>

<span data-ttu-id="8a7f2-507">El paquete de proveedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="8a7f2-508">Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="8a7f2-509">Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="8a7f2-510">El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="8a7f2-511">Si usa este paquete, no tiene que instalar el proveedor de paquete.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="8a7f2-512">El paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) es para ASP.NET 4.x, no para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="8a7f2-513">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8a7f2-514">Información general de Application Insights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="8a7f2-515">[Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="8a7f2-516">[ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="8a7f2-517">[Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="8a7f2-518">[Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="8a7f2-519">Proveedores de registro de terceros</span><span class="sxs-lookup"><span data-stu-id="8a7f2-519">Third-party logging providers</span></span>

<span data-ttu-id="8a7f2-520">Plataformas de registro de terceros que funcionan con ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="8a7f2-521">[elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="8a7f2-523">[JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="8a7f2-524">[KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="8a7f2-525">[Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="8a7f2-526">[Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-527">[NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repositorio de GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="8a7f2-529">[Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="8a7f2-530">[Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="8a7f2-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="8a7f2-532">Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8a7f2-533">El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="8a7f2-534">Agregue un paquete NuGet al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="8a7f2-535">Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="8a7f2-536">Para más información, vea la documentación de cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="8a7f2-537">Microsoft no admite los proveedores de registro de terceros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="8a7f2-538">Aplicación de consola que no es de host</span><span class="sxs-lookup"><span data-stu-id="8a7f2-538">Non-host console app</span></span>

<span data-ttu-id="8a7f2-539">Para ver un ejemplo de cómo usar el host genérico en una aplicación de consola que no sea web, vea el archivo *Program.cs* de la [aplicación de ejemplo de tareas en segundo plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="8a7f2-540">El código de registro para las aplicaciones sin un host genérico es distinto en la forma en que se [agregan los proveedores](#add-providers) y [se crean los registradores](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="8a7f2-541">Proveedores de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-541">Logging providers</span></span>

<span data-ttu-id="8a7f2-542">En una aplicación de consola que no sea de host, llame al método de extensión `Add{provider name}` del proveedor al crear un elemento `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="8a7f2-543">Creación de registros</span><span class="sxs-lookup"><span data-stu-id="8a7f2-543">Create logs</span></span>

<span data-ttu-id="8a7f2-544">Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="8a7f2-545">Use `LoggerFactory` para crear `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="8a7f2-546">En el ejemplo siguiente, se crea un registrador con `LoggingConsoleApp.Program` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="8a7f2-547">En los siguientes ejemplos de ASP.NET Core, el registrador se usa para crear registros con nivel de `Information`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="8a7f2-548">El *nivel* de registro indica la gravedad del evento registrado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="8a7f2-549">Los [niveles](#log-level) y las [categorías](#log-category) se explican con más detalle en este documento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="8a7f2-550">Registro durante la construcción del host</span><span class="sxs-lookup"><span data-stu-id="8a7f2-550">Log during host construction</span></span>

<span data-ttu-id="8a7f2-551">No se admite directamente el registro durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="8a7f2-552">Sin embargo, se puede usar un registrador independiente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-552">However, a separate logger can be used.</span></span> <span data-ttu-id="8a7f2-553">En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="8a7f2-554">`AddSerilog` usa la configuración estática especificada en `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="8a7f2-555">Configuración de un servicio que dependa de ILogger</span><span class="sxs-lookup"><span data-stu-id="8a7f2-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="8a7f2-556">La inserción del constructor de un registrador en `Startup` funciona en versiones anteriores de ASP.NET Core, ya que se crea un contenedor de inserción de dependencias independiente para el host de web.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="8a7f2-557">Para conocer por qué solo se crea un contenedor para el host genérico, vea el [anuncio de cambios importantes](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="8a7f2-558">Para configurar un servicio que dependa de `ILogger<T>`, use la inserción de constructores o proporcione un método Factory.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="8a7f2-559">Usar un Factory Method es la opción recomendada si no tiene otra alternativa.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="8a7f2-560">Por ejemplo, tomemos un servicio que necesita una instancia de `ILogger<T>` proporcionada por DI:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="8a7f2-561">El código resaltado anterior es un elemento [Func](/dotnet/api/system.func-2) que se ejecuta la primera vez que el contenedor de inserción de dependencias necesita crear una instancia de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="8a7f2-562">Puede acceder a cualquiera de los servicios registrados de esta forma.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="8a7f2-563">Creación de registros en Main</span><span class="sxs-lookup"><span data-stu-id="8a7f2-563">Create logs in Main</span></span>

<span data-ttu-id="8a7f2-564">El código siguiente registra en `Main` mediante la obtención de una instancia de `ILogger` de inserción de dependencias después de compilar el host:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="8a7f2-565">Creación de registros durante el inicio</span><span class="sxs-lookup"><span data-stu-id="8a7f2-565">Create logs in Startup</span></span>

<span data-ttu-id="8a7f2-566">El código siguiente escribe registros en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="8a7f2-567">No se admite la escritura de registros antes de la finalización del contenedor de inserción de dependencias configurado en el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="8a7f2-568">No se admite la inyección del registrador en el constructor `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="8a7f2-569">No se admite la inyección del registrador en la signatura del método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="8a7f2-570">El motivo de esta restricción es que los registros dependen de la inserción de dependencias y de la configuración, que a su vez depende de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="8a7f2-571">El contenedor de inserción de dependencias no se configura hasta que finaliza `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="8a7f2-572">Para obtener información sobre la configuración de un servicio que depende de `ILogger<T>` o sobre el motivo de que la inserción de constructor de un registrador en `Startup` funcionase en versiones anteriores, vea [Configuración de un servicio que depende de ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="8a7f2-573">No hay métodos de registrador asincrónicos</span><span class="sxs-lookup"><span data-stu-id="8a7f2-573">No asynchronous logger methods</span></span>

<span data-ttu-id="8a7f2-574">El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="8a7f2-575">Si el almacén de datos de registro es lento, no escriba directamente en él.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="8a7f2-576">Considere la posibilidad de escribir primero los mensajes de registro en un almacén rápido y, después, moverlos al almacén lento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="8a7f2-577">Por ejemplo, al iniciar sesión en SQL Server, no lo haga directamente en un método `Log`, ya que los métodos `Log` son sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="8a7f2-578">En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="8a7f2-579">Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="8a7f2-580">Cambio de los niveles de registro en una aplicación en ejecución</span><span class="sxs-lookup"><span data-stu-id="8a7f2-580">Change log levels in a running app</span></span>

<span data-ttu-id="8a7f2-581">La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="8a7f2-582">No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="8a7f2-583">Por ejemplo, el [Proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) vuelve a cargar la configuración de registro de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="8a7f2-584">Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="8a7f2-585">ILogger e ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="8a7f2-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="8a7f2-586">Las implementaciones y las interfaces de <xref:Microsoft.Extensions.Logging.ILogger%601> y <xref:Microsoft.Extensions.Logging.ILoggerFactory> se incluyen en el SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="8a7f2-587">También están disponibles en los siguientes paquetes NuGet:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="8a7f2-588">Las interfaces están en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="8a7f2-589">Las implementaciones predeterminadas se encuentran en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="8a7f2-590">Aplicación de reglas de filtro en el código</span><span class="sxs-lookup"><span data-stu-id="8a7f2-590">Apply log filter rules in code</span></span>

<span data-ttu-id="8a7f2-591">El método preferido para establecer las reglas de filtro de registro es mediante la [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="8a7f2-592">En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="8a7f2-593">`logging.AddFilter("System", LogLevel.Debug)` especifica la categoría `System` y el nivel de registro `Debug`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="8a7f2-594">El filtro se aplica a todos los proveedores porque no se ha configurado un proveedor específico.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="8a7f2-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` especifica:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="8a7f2-596">El proveedor de registro `Debug`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="8a7f2-597">Nivel de registro `Information` y superiores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="8a7f2-598">Todas las categorías que empiezan con `"Microsoft"`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="8a7f2-599">Creación de un registrador personalizado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-599">Create a custom logger</span></span>

<span data-ttu-id="8a7f2-600">Para agregar un registrador personalizado, agregue <xref:Microsoft.Extensions.Logging.ILoggerProvider> con <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="8a7f2-601">`ILoggerProvider` crea una o más instancias de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="8a7f2-602">El marco de trabajo usa las instancias de `ILogger` para registrar la información.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="8a7f2-603">Configuración de registrador personalizado de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8a7f2-603">Sample custom logger configuration</span></span>

<span data-ttu-id="8a7f2-604">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-604">The sample:</span></span>

* <span data-ttu-id="8a7f2-605">Está diseñado para ser un ejemplo muy básico que establece el color de la consola de registro por el identificador de evento y el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="8a7f2-606">Normalmente, los registradores no cambian por identificador de evento y no son específicos del nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="8a7f2-607">Crea diferentes entradas de consola de color por nivel de registro e identificador de evento con el siguiente tipo de configuración:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="8a7f2-608">El código anterior establece el nivel predeterminado en `Warning` y el color en `Yellow`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="8a7f2-609">Si `EventId` se establece en 0, se registrarán todos los eventos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="8a7f2-610">Creación del registrador personalizado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-610">Create the custom logger</span></span>

<span data-ttu-id="8a7f2-611">El nombre de la categoría de implementación `ILogger` es normalmente el origen del registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="8a7f2-612">Por ejemplo, el tipo en el que se crea el registrador:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="8a7f2-613">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-613">The preceding code:</span></span>

* <span data-ttu-id="8a7f2-614">Crea una instancia del registrador por nombre de categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="8a7f2-615">Comprueba `logLevel == _config.LogLevel` en `IsEnabled`, por lo que cada `logLevel` tiene un registrador único.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="8a7f2-616">Por lo general, los registradores también se deben habilitar para todos los niveles de registro superiores:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="8a7f2-617">Creación del proveedor LoggerProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="8a7f2-618">`LoggerProvider` es la clase que crea las instancias del registrador.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="8a7f2-619">Tal vez no sea necesario crear una instancia del registrador por categoría, pero esto tiene sentido para algunos registradores, como NLog o log4net.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="8a7f2-620">Al hacerlo, también se podrán elegir diferentes destinos de salida de registro por categoría, en caso necesario:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="8a7f2-621">En el código anterior, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> crea una única instancia de `ColorConsoleLogger` por nombre de categoría y la almacena en [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="8a7f2-622">Uso y registro del registrador personalizado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="8a7f2-623">Registre el registrador en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="8a7f2-624">Para el código anterior, proporcione al menos un método de extensión para `ILoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="8a7f2-625">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8a7f2-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="8a7f2-626">Los errores de registro deben crearse en el repositorio [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a7f2-627">Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8a7f2-628">.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8a7f2-629">En este artículo se muestra cómo usar las API de registro con proveedores integrados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="8a7f2-630">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="8a7f2-631">Incorporación de proveedores</span><span class="sxs-lookup"><span data-stu-id="8a7f2-631">Add providers</span></span>

<span data-ttu-id="8a7f2-632">Un proveedor de registro muestra o almacena registros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="8a7f2-633">Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="8a7f2-634">Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="8a7f2-635">Para usar un proveedor, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="8a7f2-636">El código anterior requiere referencias a `Microsoft.Extensions.Logging` y `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="8a7f2-637">La plantilla de proyecto predeterminada llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="8a7f2-638">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-638">Console</span></span>
* <span data-ttu-id="8a7f2-639">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-639">Debug</span></span>
* <span data-ttu-id="8a7f2-640">EventSource (a partir de ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="8a7f2-641">Si usa `CreateDefaultBuilder`, puede reemplazar los proveedores predeterminados por sus propios valores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="8a7f2-642">Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="8a7f2-643">Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="8a7f2-644">Creación de registros</span><span class="sxs-lookup"><span data-stu-id="8a7f2-644">Create logs</span></span>

<span data-ttu-id="8a7f2-645">Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="8a7f2-646">En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="8a7f2-647">En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="8a7f2-648">En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="8a7f2-649">La *categoría* de registro es una cadena que está asociada con cada registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="8a7f2-650">La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="8a7f2-651">En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="8a7f2-652">El *nivel* de registro indica la gravedad del evento registrado.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="8a7f2-653">Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="8a7f2-654">Creación de registros durante el inicio</span><span class="sxs-lookup"><span data-stu-id="8a7f2-654">Create logs in Startup</span></span>

<span data-ttu-id="8a7f2-655">Para escribir registros en la clase `Startup`, incluya un parámetro `ILogger` en la signatura de construcción:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="8a7f2-656">Crear registros en la clase del programa</span><span class="sxs-lookup"><span data-stu-id="8a7f2-656">Create logs in the Program class</span></span>

<span data-ttu-id="8a7f2-657">Para escribir registros la clase `Program`, obtenga una instancia `ILogger` de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="8a7f2-658">No se admite directamente el registro durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="8a7f2-659">Sin embargo, se puede usar un registrador independiente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-659">However, a separate logger can be used.</span></span> <span data-ttu-id="8a7f2-660">En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="8a7f2-661">`AddSerilog` usa la configuración estática especificada en `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="8a7f2-662">No hay métodos de registrador asincrónicos</span><span class="sxs-lookup"><span data-stu-id="8a7f2-662">No asynchronous logger methods</span></span>

<span data-ttu-id="8a7f2-663">El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="8a7f2-664">Si el almacén de datos de registro es lento, no escriba directamente en él.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="8a7f2-665">Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="8a7f2-666">Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="8a7f2-667">En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="8a7f2-668">Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="8a7f2-669">Configuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-669">Configuration</span></span>

<span data-ttu-id="8a7f2-670">Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="8a7f2-671">Formatos de archivo (INI, JSON y XML).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="8a7f2-672">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-672">Command-line arguments.</span></span>
* <span data-ttu-id="8a7f2-673">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-673">Environment variables.</span></span>
* <span data-ttu-id="8a7f2-674">Objetos de .NET en memoria.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="8a7f2-675">El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="8a7f2-676">Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="8a7f2-677">Proveedores personalizados (instalados o creados).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="8a7f2-678">Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="8a7f2-679">En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="8a7f2-680">La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="8a7f2-681">La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="8a7f2-682">En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="8a7f2-683">Otras propiedades bajo `Logging` especifican proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="8a7f2-684">El ejemplo es para el proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-684">The example is for the Console provider.</span></span> <span data-ttu-id="8a7f2-685">Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="8a7f2-686">Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="8a7f2-687">`LogLevel` en un proveedor especifica niveles de registro para ese proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="8a7f2-688">Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="8a7f2-689">Por ejemplo, tomemos el siguiente código JSON:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="8a7f2-690">En el JSON anterior, la configuración del proveedor `Console` invalida el nivel de registro anterior (predeterminado).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="8a7f2-691">La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="8a7f2-692">No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="8a7f2-693">Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="8a7f2-694">Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="8a7f2-695">Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="8a7f2-696">Salida de registro de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8a7f2-696">Sample logging output</span></span>

<span data-ttu-id="8a7f2-697">Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="8a7f2-698">Este es un ejemplo de salida de la consola:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-698">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="8a7f2-699">Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="8a7f2-700">Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="8a7f2-701">Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApi”.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="8a7f2-702">Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="8a7f2-703">ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="8a7f2-704">En el resto de este artículo se explican algunos detalles y opciones para el registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="8a7f2-705">Paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="8a7f2-705">NuGet packages</span></span>

<span data-ttu-id="8a7f2-706">Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="8a7f2-707">Categoría de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-707">Log category</span></span>

<span data-ttu-id="8a7f2-708">Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="8a7f2-709">Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="8a7f2-710">La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="8a7f2-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="8a7f2-711">Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="8a7f2-712">Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="8a7f2-713">`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="8a7f2-714">Nivel de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-714">Log level</span></span>

<span data-ttu-id="8a7f2-715">Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="8a7f2-716">El nivel de registro indica la gravedad o importancia.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="8a7f2-717">Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="8a7f2-718">El siguiente código crea los registros `Information` y `Warning`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="8a7f2-719">En el código anterior, los parámetros `MyLogEvents.GetItem` y `MyLogEvents.GetItemNotFound` son el [id. de evento de registro](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="8a7f2-720">El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="8a7f2-721">Los parámetros de método se explican detalladamente en la [sección Plantilla de mensaje de registro](#lmt) en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="8a7f2-722">Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="8a7f2-723">Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="8a7f2-724">Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="8a7f2-725">Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="8a7f2-726">ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="8a7f2-727">Seguimiento = 0</span><span class="sxs-lookup"><span data-stu-id="8a7f2-727">Trace = 0</span></span>

  <span data-ttu-id="8a7f2-728">Para información que normalmente solo es útil para la depuración.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="8a7f2-729">Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="8a7f2-730">*Deshabilitado de forma predeterminada.*</span><span class="sxs-lookup"><span data-stu-id="8a7f2-730">*Disabled by default.*</span></span>

* <span data-ttu-id="8a7f2-731">Depurar = 1</span><span class="sxs-lookup"><span data-stu-id="8a7f2-731">Debug = 1</span></span>

  <span data-ttu-id="8a7f2-732">Para información que puede ser útil para el desarrollo y la depuración.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="8a7f2-733">Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="8a7f2-734">Información = 2</span><span class="sxs-lookup"><span data-stu-id="8a7f2-734">Information = 2</span></span>

  <span data-ttu-id="8a7f2-735">Para realizar el seguimiento del flujo general de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="8a7f2-736">Estos registros suelen tener algún valor a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="8a7f2-737">Ejemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="8a7f2-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="8a7f2-738">Advertencia = 3</span><span class="sxs-lookup"><span data-stu-id="8a7f2-738">Warning = 3</span></span>

  <span data-ttu-id="8a7f2-739">Para los eventos anómalos o inesperados en el flujo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="8a7f2-740">Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="8a7f2-741">Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="8a7f2-742">Ejemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="8a7f2-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="8a7f2-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="8a7f2-743">Error = 4</span></span>

  <span data-ttu-id="8a7f2-744">Para los errores y excepciones que no se pueden controlar.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="8a7f2-745">Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="8a7f2-746">Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="8a7f2-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="8a7f2-747">Crítico = 5</span><span class="sxs-lookup"><span data-stu-id="8a7f2-747">Critical = 5</span></span>

  <span data-ttu-id="8a7f2-748">Para los errores que requieren atención inmediata.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-748">For failures that require immediate attention.</span></span> <span data-ttu-id="8a7f2-749">Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="8a7f2-750">Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="8a7f2-751">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-751">For example:</span></span>

* <span data-ttu-id="8a7f2-752">En producción:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-752">In production:</span></span>
  * <span data-ttu-id="8a7f2-753">El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="8a7f2-754">Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="8a7f2-755">El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="8a7f2-756">Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="8a7f2-757">Durante el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-757">During development:</span></span>
  * <span data-ttu-id="8a7f2-758">Registre los mensajes `Warning` a `Critical` en la consola.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="8a7f2-759">Agregue los mensajes `Trace` a `Information` al solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="8a7f2-760">En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="8a7f2-761">ASP.NET Core escribe registros de eventos de marco.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="8a7f2-762">En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="8a7f2-763">Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="8a7f2-764">Id. de evento del registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-764">Log event ID</span></span>

<span data-ttu-id="8a7f2-765">Cada registro se puede especificar un *id. de evento*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="8a7f2-766">La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="8a7f2-767">Un id. de evento asocia un conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-767">An event ID associates a set of events.</span></span> <span data-ttu-id="8a7f2-768">Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="8a7f2-769">El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="8a7f2-770">El proveedor de depuración no muestra los identificadores de evento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="8a7f2-771">El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="8a7f2-772">Plantilla de mensaje de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-772">Log message template</span></span>

<span data-ttu-id="8a7f2-773">Cada registro especifica una plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-773">Each log specifies a message template.</span></span> <span data-ttu-id="8a7f2-774">La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="8a7f2-775">Use los nombres de los marcadores de posición, no números.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="8a7f2-776">El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="8a7f2-777">En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="8a7f2-778">Este código crea un mensaje de registro con los valores de parámetro en secuencia:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="8a7f2-779">La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="8a7f2-780">Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="8a7f2-781">Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="8a7f2-782">Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="8a7f2-783">Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="8a7f2-784">Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="8a7f2-785">Excepciones de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-785">Logging exceptions</span></span>

<span data-ttu-id="8a7f2-786">Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="8a7f2-787">Cada proveedor controla la información de la excepción de maneras diferentes.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="8a7f2-788">Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="8a7f2-789">Filtrado del registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-789">Log filtering</span></span>

<span data-ttu-id="8a7f2-790">Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="8a7f2-791">Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="8a7f2-792">Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="8a7f2-793">El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="8a7f2-794">Creación de reglas de filtro en la configuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-794">Create filter rules in configuration</span></span>

<span data-ttu-id="8a7f2-795">El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="8a7f2-796">El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="8a7f2-797">Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="8a7f2-798">Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="8a7f2-799">Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="8a7f2-800">Reglas de filtro en el código</span><span class="sxs-lookup"><span data-stu-id="8a7f2-800">Filter rules in code</span></span>

<span data-ttu-id="8a7f2-801">En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="8a7f2-802">El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="8a7f2-803">El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="8a7f2-804">Cómo se aplican las reglas de filtro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-804">How filtering rules are applied</span></span>

<span data-ttu-id="8a7f2-805">Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="8a7f2-806">Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="8a7f2-807">número</span><span class="sxs-lookup"><span data-stu-id="8a7f2-807">Number</span></span> | <span data-ttu-id="8a7f2-808">Proveedor</span><span class="sxs-lookup"><span data-stu-id="8a7f2-808">Provider</span></span>      | <span data-ttu-id="8a7f2-809">Categorías que comienzan por...</span><span class="sxs-lookup"><span data-stu-id="8a7f2-809">Categories that begin with ...</span></span>          | <span data-ttu-id="8a7f2-810">Nivel de registro mínimo</span><span class="sxs-lookup"><span data-stu-id="8a7f2-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="8a7f2-811">1</span><span class="sxs-lookup"><span data-stu-id="8a7f2-811">1</span></span>      | <span data-ttu-id="8a7f2-812">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-812">Debug</span></span>         | <span data-ttu-id="8a7f2-813">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="8a7f2-813">All categories</span></span>                          | <span data-ttu-id="8a7f2-814">Información</span><span class="sxs-lookup"><span data-stu-id="8a7f2-814">Information</span></span>       |
| <span data-ttu-id="8a7f2-815">2</span><span class="sxs-lookup"><span data-stu-id="8a7f2-815">2</span></span>      | <span data-ttu-id="8a7f2-816">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-816">Console</span></span>       | <span data-ttu-id="8a7f2-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="8a7f2-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="8a7f2-818">Advertencia</span><span class="sxs-lookup"><span data-stu-id="8a7f2-818">Warning</span></span>           |
| <span data-ttu-id="8a7f2-819">3</span><span class="sxs-lookup"><span data-stu-id="8a7f2-819">3</span></span>      | <span data-ttu-id="8a7f2-820">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-820">Console</span></span>       | <span data-ttu-id="8a7f2-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="8a7f2-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="8a7f2-822">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-822">Debug</span></span>             |
| <span data-ttu-id="8a7f2-823">4</span><span class="sxs-lookup"><span data-stu-id="8a7f2-823">4</span></span>      | <span data-ttu-id="8a7f2-824">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-824">Console</span></span>       | <span data-ttu-id="8a7f2-825">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="8a7f2-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="8a7f2-826">Error</span><span class="sxs-lookup"><span data-stu-id="8a7f2-826">Error</span></span>             |
| <span data-ttu-id="8a7f2-827">5</span><span class="sxs-lookup"><span data-stu-id="8a7f2-827">5</span></span>      | <span data-ttu-id="8a7f2-828">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-828">Console</span></span>       | <span data-ttu-id="8a7f2-829">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="8a7f2-829">All categories</span></span>                          | <span data-ttu-id="8a7f2-830">Información</span><span class="sxs-lookup"><span data-stu-id="8a7f2-830">Information</span></span>       |
| <span data-ttu-id="8a7f2-831">6</span><span class="sxs-lookup"><span data-stu-id="8a7f2-831">6</span></span>      | <span data-ttu-id="8a7f2-832">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="8a7f2-832">All providers</span></span> | <span data-ttu-id="8a7f2-833">Todas las categorías</span><span class="sxs-lookup"><span data-stu-id="8a7f2-833">All categories</span></span>                          | <span data-ttu-id="8a7f2-834">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-834">Debug</span></span>             |
| <span data-ttu-id="8a7f2-835">7</span><span class="sxs-lookup"><span data-stu-id="8a7f2-835">7</span></span>      | <span data-ttu-id="8a7f2-836">Todos los proveedores</span><span class="sxs-lookup"><span data-stu-id="8a7f2-836">All providers</span></span> | <span data-ttu-id="8a7f2-837">Sistema</span><span class="sxs-lookup"><span data-stu-id="8a7f2-837">System</span></span>                                  | <span data-ttu-id="8a7f2-838">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-838">Debug</span></span>             |
| <span data-ttu-id="8a7f2-839">8</span><span class="sxs-lookup"><span data-stu-id="8a7f2-839">8</span></span>      | <span data-ttu-id="8a7f2-840">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-840">Debug</span></span>         | <span data-ttu-id="8a7f2-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="8a7f2-841">Microsoft</span></span>                               | <span data-ttu-id="8a7f2-842">Seguimiento</span><span class="sxs-lookup"><span data-stu-id="8a7f2-842">Trace</span></span>             |

<span data-ttu-id="8a7f2-843">Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="8a7f2-844">Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="8a7f2-845">De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="8a7f2-846">Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="8a7f2-847">Se seleccionan todas las reglas que coinciden con el proveedor o su alias.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="8a7f2-848">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="8a7f2-849">Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="8a7f2-850">Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="8a7f2-851">Si se seleccionan varias reglas, se toma la **última**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="8a7f2-852">Si no se selecciona ninguna regla, se usa `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="8a7f2-853">Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="8a7f2-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="8a7f2-854">Para el proveedor de depuración, se aplican las reglas 1, 6 y 8.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="8a7f2-855">La regla 8 es la más específica, por lo que se selecciona.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="8a7f2-856">Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="8a7f2-857">La regla 3 es la más específica.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="8a7f2-858">La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="8a7f2-859">Los registros de nivel `Debug` y superiores se envían al proveedor de consola.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="8a7f2-860">Alias de proveedor</span><span class="sxs-lookup"><span data-stu-id="8a7f2-860">Provider aliases</span></span>

<span data-ttu-id="8a7f2-861">Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="8a7f2-862">Para los proveedores integrados, use los alias siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="8a7f2-863">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-863">Console</span></span>
* <span data-ttu-id="8a7f2-864">Depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-864">Debug</span></span>
* <span data-ttu-id="8a7f2-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-865">EventSource</span></span>
* <span data-ttu-id="8a7f2-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="8a7f2-866">EventLog</span></span>
* <span data-ttu-id="8a7f2-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-867">TraceSource</span></span>
* <span data-ttu-id="8a7f2-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="8a7f2-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="8a7f2-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="8a7f2-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="8a7f2-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="8a7f2-871">Nivel mínimo predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a7f2-871">Default minimum level</span></span>

<span data-ttu-id="8a7f2-872">Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="8a7f2-873">En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="8a7f2-874">Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="8a7f2-875">Funciones de filtro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-875">Filter functions</span></span>

<span data-ttu-id="8a7f2-876">Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="8a7f2-877">El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="8a7f2-878">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="8a7f2-879">Niveles y categorías del sistema</span><span class="sxs-lookup"><span data-stu-id="8a7f2-879">System categories and levels</span></span>

<span data-ttu-id="8a7f2-880">Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="8a7f2-881">Categoría</span><span class="sxs-lookup"><span data-stu-id="8a7f2-881">Category</span></span>                            | <span data-ttu-id="8a7f2-882">Notas</span><span class="sxs-lookup"><span data-stu-id="8a7f2-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="8a7f2-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="8a7f2-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="8a7f2-884">Diagnósticos generales de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="8a7f2-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="8a7f2-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="8a7f2-886">Qué claves se tuvieron en cuenta, encontraron y usaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="8a7f2-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="8a7f2-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="8a7f2-888">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-888">Hosts allowed.</span></span> |
| <span data-ttu-id="8a7f2-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="8a7f2-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="8a7f2-890">Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="8a7f2-891">Qué ensamblados de inicio de hospedaje se cargaron.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="8a7f2-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="8a7f2-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="8a7f2-893">Diagnósticos de MVC y Razor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="8a7f2-894">Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="8a7f2-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="8a7f2-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="8a7f2-896">Información de coincidencia de ruta.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-896">Route matching information.</span></span> |
| <span data-ttu-id="8a7f2-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="8a7f2-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="8a7f2-898">Inicio y detención de conexión y mantener las respuestas activas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="8a7f2-899">Información de certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="8a7f2-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="8a7f2-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="8a7f2-901">Archivos servidos.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-901">Files served.</span></span> |
| <span data-ttu-id="8a7f2-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8a7f2-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="8a7f2-903">Diagnósticos generales de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="8a7f2-904">Actividad y la configuración de bases de datos, detección de cambios y migraciones.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="8a7f2-905">Ámbitos de registro</span><span class="sxs-lookup"><span data-stu-id="8a7f2-905">Log scopes</span></span>

 <span data-ttu-id="8a7f2-906">Un *ámbito* puede agrupar un conjunto de operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="8a7f2-907">Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="8a7f2-908">Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="8a7f2-909">Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="8a7f2-910">Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="8a7f2-911">El código siguiente permite ámbitos para el proveedor de la consola:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="8a7f2-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="8a7f2-913">Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="8a7f2-914">Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="8a7f2-915">Cada mensaje de registro incluye la información de ámbito:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="8a7f2-916">Proveedores de registro integrados</span><span class="sxs-lookup"><span data-stu-id="8a7f2-916">Built-in logging providers</span></span>

<span data-ttu-id="8a7f2-917">ASP.NET Core incluye los proveedores siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="8a7f2-918">Consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="8a7f2-919">Depurar</span><span class="sxs-lookup"><span data-stu-id="8a7f2-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="8a7f2-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="8a7f2-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="8a7f2-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="8a7f2-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="8a7f2-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="8a7f2-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="8a7f2-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="8a7f2-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="8a7f2-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="8a7f2-926">Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="8a7f2-927">Proveedor de la consola</span><span class="sxs-lookup"><span data-stu-id="8a7f2-927">Console provider</span></span>

<span data-ttu-id="8a7f2-928">El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="8a7f2-929">Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="8a7f2-930">Proveedor de depuración</span><span class="sxs-lookup"><span data-stu-id="8a7f2-930">Debug provider</span></span>

<span data-ttu-id="8a7f2-931">El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="8a7f2-932">En Linux, este proveedor escribe registros en */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="8a7f2-933">Proveedor de origen del evento</span><span class="sxs-lookup"><span data-stu-id="8a7f2-933">Event Source provider</span></span>

<span data-ttu-id="8a7f2-934">El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="8a7f2-935">En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="8a7f2-936">El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="8a7f2-937">Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="8a7f2-938">Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="8a7f2-939">Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="8a7f2-940">(No olvide el asterisco al principio de la cadena).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="8a7f2-942">Proveedor EventLog de Windows</span><span class="sxs-lookup"><span data-stu-id="8a7f2-942">Windows EventLog provider</span></span>

<span data-ttu-id="8a7f2-943">El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="8a7f2-944">Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="8a7f2-945">Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="8a7f2-946">`LogName`: "Application"</span><span class="sxs-lookup"><span data-stu-id="8a7f2-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="8a7f2-947">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="8a7f2-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="8a7f2-948">`MachineName`: se usa el nombre del equipo local.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="8a7f2-949">Los eventos se registran para el [Nivel de advertencia y superior](#log-level).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="8a7f2-950">En el ejemplo siguiente se establece el nivel de registro predeterminado del Registro de eventos en <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="8a7f2-951">Proveedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="8a7f2-951">TraceSource provider</span></span>

<span data-ttu-id="8a7f2-952">El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="8a7f2-953">[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="8a7f2-954">Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="8a7f2-955">El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="8a7f2-956">Proveedor Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a7f2-956">Azure App Service provider</span></span>

<span data-ttu-id="8a7f2-957">El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="8a7f2-958">El paquete de proveedor no está incluido en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="8a7f2-959">Si el destino es .NET Framework o hace referencia al metapaquete `Microsoft.AspNetCore.App`, agregue el paquete del proveedor al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="8a7f2-960">Una sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite pasar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="8a7f2-961">El objeto de configuración puede invalidar la configuración predeterminada, como la plantilla de salida de registro, el nombre de blob y el límite de tamaño de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="8a7f2-962">(La *plantilla salida* es una plantilla de mensaje que se aplica a todos los registros además de que se proporciona con una llamada de método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="8a7f2-963">Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="8a7f2-964">Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="8a7f2-965">**Registro de la aplicación (sistema de archivos)**</span><span class="sxs-lookup"><span data-stu-id="8a7f2-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="8a7f2-966">**Registro de la aplicación (blob)**</span><span class="sxs-lookup"><span data-stu-id="8a7f2-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="8a7f2-967">La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="8a7f2-968">El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="8a7f2-969">El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="8a7f2-970">El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="8a7f2-971">No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="8a7f2-972">Secuencias de registro de Azure</span><span class="sxs-lookup"><span data-stu-id="8a7f2-972">Azure log streaming</span></span>

<span data-ttu-id="8a7f2-973">Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="8a7f2-974">El servidor de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="8a7f2-974">The app server</span></span>
* <span data-ttu-id="8a7f2-975">El servidor web</span><span class="sxs-lookup"><span data-stu-id="8a7f2-975">The web server</span></span>
* <span data-ttu-id="8a7f2-976">Error del seguimiento de solicitudes</span><span class="sxs-lookup"><span data-stu-id="8a7f2-976">Failed request tracing</span></span>

<span data-ttu-id="8a7f2-977">Para configurar las secuencias de registro de Azure:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="8a7f2-978">Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="8a7f2-979">Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="8a7f2-980">Elija el **Nivel** de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-980">Choose the log **Level**.</span></span> <span data-ttu-id="8a7f2-981">Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="8a7f2-982">Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="8a7f2-983">La aplicación los registra a través de la interfaz `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="8a7f2-984">Registro de seguimiento de Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="8a7f2-985">El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="8a7f2-986">Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="8a7f2-987">Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="8a7f2-988">El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="8a7f2-989">Si usa este paquete, no tiene que instalar el proveedor de paquete.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="8a7f2-990">No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="8a7f2-991">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8a7f2-992">Información general de Application Insights</span><span class="sxs-lookup"><span data-stu-id="8a7f2-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="8a7f2-993">[Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="8a7f2-994">[ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="8a7f2-995">[Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)</span><span class="sxs-lookup"><span data-stu-id="8a7f2-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="8a7f2-996">[Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="8a7f2-997">Proveedores de registro de terceros</span><span class="sxs-lookup"><span data-stu-id="8a7f2-997">Third-party logging providers</span></span>

<span data-ttu-id="8a7f2-998">Plataformas de registro de terceros que funcionan con ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="8a7f2-999">[elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="8a7f2-1001">[JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="8a7f2-1002">[KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="8a7f2-1003">[Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="8a7f2-1004">[Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-1005">[NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="8a7f2-1006">[Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="8a7f2-1007">[Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="8a7f2-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="8a7f2-1009">Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8a7f2-1010">El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="8a7f2-1011">Agregue un paquete NuGet al proyecto.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="8a7f2-1012">Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="8a7f2-1013">Para más información, vea la documentación de cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="8a7f2-1014">Microsoft no admite los proveedores de registro de terceros.</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a7f2-1015">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8a7f2-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
