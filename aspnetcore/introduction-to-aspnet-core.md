---
title: Introducción a ASP.NET Core
author: rick-anderson
description: Obtenga una introducción a ASP.NET Core, un marco multiplataforma de código abierto y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: index
ms.openlocfilehash: 4301e0d59364573767ab4cae25a4818ff84b9abc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052232"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="00404-103">Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00404-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="00404-104">Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="00404-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="00404-105">ASP.NET Core es un marco multiplataforma de [código abierto](https://github.com/dotnet/aspnetcore) y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube.</span><span class="sxs-lookup"><span data-stu-id="00404-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="00404-106">Con ASP.NET Core puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="00404-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="00404-107">Compilar servicios y aplicaciones web, aplicaciones de [Internet de las cosas (IoT)](https://www.microsoft.com/internet-of-things/) y back-ends móviles.</span><span class="sxs-lookup"><span data-stu-id="00404-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="00404-108">Usar sus herramientas de desarrollo favoritas en Windows, macOS y Linux.</span><span class="sxs-lookup"><span data-stu-id="00404-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="00404-109">Efectuar implementaciones locales y en la nube.</span><span class="sxs-lookup"><span data-stu-id="00404-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="00404-110">Ejecutar en [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="00404-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="00404-111">¿Por qué elegir ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="00404-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="00404-112">Millones de desarrolladores usan o han usado [ASP.NET 4.x](/aspnet/overview) para crear aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="00404-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="00404-113">ASP.NET Core es un nuevo diseño de ASP.NET 4.x que incluye cambios en la arquitectura que dan como resultado un marco más sencillo y modular.</span><span class="sxs-lookup"><span data-stu-id="00404-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="00404-114">Creación de API web e interfaces de usuario web mediante ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="00404-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="00404-115">ASP.NET Core MVC proporciona características para crear [API web](xref:tutorials/first-web-api) y [aplicaciones web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="00404-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="00404-116">El [patrón Modelo-Vista-Controlador (MVC)](xref:mvc/overview) permite que se puedan hacer pruebas en las API web y en las aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="00404-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="00404-117">[Razor Pages](xref:razor-pages/index) es un modelo de programación basado en páginas que facilita la compilación de interfaces de usuario web y hace que sea más productiva.</span><span class="sxs-lookup"><span data-stu-id="00404-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="00404-118">El [marcado de RazorRazor](xref:mvc/views/razor) proporciona una sintaxis productiva para [Razor Pages](xref:razor-pages/index) y las [vistas de MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="00404-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="00404-119">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="00404-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="00404-120">La compatibilidad integrada para [varios formatos de datos y la negociación de contenidos](xref:web-api/advanced/formatting) permite que las API web lleguen a una amplia gama de clientes, como los exploradores y los dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="00404-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="00404-121">El [enlace de modelo](xref:mvc/models/model-binding) asigna automáticamente datos de solicitudes HTTP a parámetros de método de acción.</span><span class="sxs-lookup"><span data-stu-id="00404-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="00404-122">La [validación de modelos](xref:mvc/models/validation) efectúa una validación del lado cliente y del lado servidor de forma automática.</span><span class="sxs-lookup"><span data-stu-id="00404-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="00404-123">Desarrollo del lado del cliente</span><span class="sxs-lookup"><span data-stu-id="00404-123">Client-side development</span></span>

<span data-ttu-id="00404-124">ASP.NET Core se integra perfectamente con bibliotecas y plataformas de trabajo populares del lado cliente, que incluyen [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) y [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="00404-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="00404-125">Para más información, consulte <xref:blazor/index> y los temas relacionados en *Client-side development* (Desarrollo del lado cliente).</span><span class="sxs-lookup"><span data-stu-id="00404-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="00404-126">Plataformas de destino de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00404-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="00404-127">ASP.NET Core 3.x y las versiones posteriores solo pueden tener como destino .NET Core.</span><span class="sxs-lookup"><span data-stu-id="00404-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="00404-128">Por lo general, ASP.NET Core se compone de bibliotecas de [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="00404-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="00404-129">Las bibliotecas escritas con .NET Standard 2.0 se ejecutan en [cualquier plataforma .NET que implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="00404-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="00404-130">El uso de .NET Core como destino cuenta con varias ventajas que van en aumento con cada versión.</span><span class="sxs-lookup"><span data-stu-id="00404-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="00404-131">Entre las ventajas del uso de .NET Core en vez de .NET Framework se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="00404-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="00404-132">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="00404-132">Cross-platform.</span></span> <span data-ttu-id="00404-133">Es compatible con Windows, macOS y Linux.</span><span class="sxs-lookup"><span data-stu-id="00404-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="00404-134">Rendimiento mejorado</span><span class="sxs-lookup"><span data-stu-id="00404-134">Improved performance</span></span>
* [<span data-ttu-id="00404-135">Control de versiones en paralelo</span><span class="sxs-lookup"><span data-stu-id="00404-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="00404-136">Nuevas API.</span><span class="sxs-lookup"><span data-stu-id="00404-136">New APIs</span></span>
* <span data-ttu-id="00404-137">Código Abierto</span><span class="sxs-lookup"><span data-stu-id="00404-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="00404-138">Ruta de aprendizaje recomendada</span><span class="sxs-lookup"><span data-stu-id="00404-138">Recommended learning path</span></span>

<span data-ttu-id="00404-139">Se recomienda realizar la siguiente secuencia de tutoriales a modo de introducción para desarrollar aplicaciones de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="00404-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="00404-140">Realice el tutorial correspondiente al tipo de aplicación que quiera desarrollar o mantener.</span><span class="sxs-lookup"><span data-stu-id="00404-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="00404-141">Tipo de aplicación</span><span class="sxs-lookup"><span data-stu-id="00404-141">App type</span></span>  |<span data-ttu-id="00404-142">Escenario</span><span class="sxs-lookup"><span data-stu-id="00404-142">Scenario</span></span>  |<span data-ttu-id="00404-143">Tutorial</span><span class="sxs-lookup"><span data-stu-id="00404-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="00404-144">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="00404-144">Web app</span></span>                   | <span data-ttu-id="00404-145">Nuevo desarrollo de interfaz de usuario web del lado servidor</span><span class="sxs-lookup"><span data-stu-id="00404-145">New server-side web UI development</span></span> |[<span data-ttu-id="00404-146">Primeros pasos con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="00404-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="00404-147">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="00404-147">Web app</span></span>                   | <span data-ttu-id="00404-148">Mantener una aplicación de MVC</span><span class="sxs-lookup"><span data-stu-id="00404-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="00404-149">Introducción a MVC</span><span class="sxs-lookup"><span data-stu-id="00404-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="00404-150">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="00404-150">Web app</span></span>                   | <span data-ttu-id="00404-151">Desarrollo de interfaz de usuario web del lado cliente</span><span class="sxs-lookup"><span data-stu-id="00404-151">Client-side web UI development</span></span> |[<span data-ttu-id="00404-152">Introducción a Blazor</span><span class="sxs-lookup"><span data-stu-id="00404-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="00404-153">Web API</span><span class="sxs-lookup"><span data-stu-id="00404-153">Web API</span></span>                   | <span data-ttu-id="00404-154">Servicios HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="00404-154">RESTful HTTP services</span></span> |<span data-ttu-id="00404-155">[Creación de una API web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="00404-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="00404-156">Aplicación de llamada a procedimiento remoto</span><span class="sxs-lookup"><span data-stu-id="00404-156">Remote Procedure Call app</span></span> | <span data-ttu-id="00404-157">Servicios de contrato primero con búferes de protocolo</span><span class="sxs-lookup"><span data-stu-id="00404-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="00404-158">Introducción a un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="00404-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="00404-159">Aplicación en tiempo real</span><span class="sxs-lookup"><span data-stu-id="00404-159">Real-time app</span></span>             | <span data-ttu-id="00404-160">Comunicación bidireccional entre servidores y clientes conectados</span><span class="sxs-lookup"><span data-stu-id="00404-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="00404-161">Introducción a SignalR</span><span class="sxs-lookup"><span data-stu-id="00404-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="00404-162">Realice un tutorial que muestra cómo realizar el acceso a datos básicos.</span><span class="sxs-lookup"><span data-stu-id="00404-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="00404-163">Escenario</span><span class="sxs-lookup"><span data-stu-id="00404-163">Scenario</span></span>  |<span data-ttu-id="00404-164">Tutorial</span><span class="sxs-lookup"><span data-stu-id="00404-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="00404-165">Nuevo desarrollo</span><span class="sxs-lookup"><span data-stu-id="00404-165">New development</span></span>        |[<span data-ttu-id="00404-166">Razor Pages con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="00404-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="00404-167">Mantener una aplicación de MVC</span><span class="sxs-lookup"><span data-stu-id="00404-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="00404-168">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="00404-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="00404-169">Lea una introducción a los [conceptos básicos](xref:fundamentals/index) de ASP.NET Core aplicables a todos los tipos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="00404-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="00404-170">Examine la tabla de contenido para ver otros temas de interés.</span><span class="sxs-lookup"><span data-stu-id="00404-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="00404-171">&dagger;También hay un [tutorial de API web interactivo](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="00404-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="00404-172">No es necesario instalar ninguna herramienta de desarrollo localmente.</span><span class="sxs-lookup"><span data-stu-id="00404-172">No local installation of development tools is required.</span></span> <span data-ttu-id="00404-173">El código se ejecuta en un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) en el explorador, y se usa [curl](https://curl.haxx.se/) para realizar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="00404-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="00404-174">Migración desde .NET Framework</span><span class="sxs-lookup"><span data-stu-id="00404-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="00404-175">Para obtener una guía de referencia para migrar aplicaciones de ASP.NET 4.x a ASP.NET Core, vea <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="00404-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="00404-176">ASP.NET Core es un marco multiplataforma de [código abierto](https://github.com/dotnet/aspnetcore) y de alto rendimiento que tiene como finalidad compilar aplicaciones modernas conectadas a Internet y habilitadas para la nube.</span><span class="sxs-lookup"><span data-stu-id="00404-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="00404-177">Con ASP.NET Core puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="00404-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="00404-178">Compilar servicios y aplicaciones web, aplicaciones de [Internet de las cosas (IoT)](https://www.microsoft.com/internet-of-things/) y back-ends móviles.</span><span class="sxs-lookup"><span data-stu-id="00404-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="00404-179">Usar sus herramientas de desarrollo favoritas en Windows, macOS y Linux.</span><span class="sxs-lookup"><span data-stu-id="00404-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="00404-180">Efectuar implementaciones locales y en la nube.</span><span class="sxs-lookup"><span data-stu-id="00404-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="00404-181">Ejecutarlo en [.NET Core o en .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="00404-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="00404-182">¿Por qué elegir ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="00404-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="00404-183">Millones de desarrolladores usan o han usado [ASP.NET 4.x](/aspnet/overview) para crear aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="00404-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="00404-184">ASP.NET Core es un nuevo diseño de ASP.NET 4.x que cuenta con cambios en la arquitectura que dan como resultado un marco más sencillo y modular.</span><span class="sxs-lookup"><span data-stu-id="00404-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="00404-185">Creación de API web e interfaces de usuario web mediante ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="00404-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="00404-186">ASP.NET Core MVC proporciona características para crear [API web](xref:tutorials/first-web-api) y [aplicaciones web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="00404-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="00404-187">El [patrón Modelo-Vista-Controlador (MVC)](xref:mvc/overview) permite que se puedan hacer pruebas en las API web y en las aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="00404-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="00404-188">[Razor Pages](xref:razor-pages/index) es un modelo de programación basado en páginas que facilita la compilación de interfaces de usuario web y hace que sea más productiva.</span><span class="sxs-lookup"><span data-stu-id="00404-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="00404-189">El [marcado de RazorRazor](xref:mvc/views/razor) proporciona una sintaxis productiva para [Razor Pages](xref:razor-pages/index) y las [vistas de MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="00404-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="00404-190">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="00404-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="00404-191">La compatibilidad integrada para [varios formatos de datos y la negociación de contenidos](xref:web-api/advanced/formatting) permite que las API web lleguen a una amplia gama de clientes, como los exploradores y los dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="00404-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="00404-192">El [enlace de modelo](xref:mvc/models/model-binding) asigna automáticamente datos de solicitudes HTTP a parámetros de método de acción.</span><span class="sxs-lookup"><span data-stu-id="00404-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="00404-193">La [validación de modelos](xref:mvc/models/validation) efectúa una validación del lado cliente y del lado servidor de forma automática.</span><span class="sxs-lookup"><span data-stu-id="00404-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="00404-194">Desarrollo del lado del cliente</span><span class="sxs-lookup"><span data-stu-id="00404-194">Client-side development</span></span>

<span data-ttu-id="00404-195">ASP.NET Core se integra perfectamente con bibliotecas y plataformas de trabajo populares del lado cliente, que incluyen [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) y [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="00404-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="00404-196">Para más información, consulte <xref:blazor/index> y los temas relacionados en *Client-side development* (Desarrollo del lado cliente).</span><span class="sxs-lookup"><span data-stu-id="00404-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="00404-197">ASP.NET Core con .NET Framework como destino</span><span class="sxs-lookup"><span data-stu-id="00404-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="00404-198">ASP.NET Core 2.x puede tener como destino .NET Core o .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="00404-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="00404-199">Las aplicaciones de ASP.NET Core que tienen como destino .NET Framework no son multiplataforma, sino que solo se ejecutan en Windows.</span><span class="sxs-lookup"><span data-stu-id="00404-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="00404-200">Por lo general, ASP.NET Core 2.x está formado por bibliotecas de [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="00404-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="00404-201">Las bibliotecas escritas con .NET Standard 2.0 se ejecutan en [cualquier plataforma .NET que implementa .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="00404-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="00404-202">ASP.NET Core 2.x se admite en las versiones de .NET Framework que implementan .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="00404-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="00404-203">Se recomienda encarecidamente usar la versión más reciente de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="00404-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="00404-204">.NET Framework 4.6.1 y posterior.</span><span class="sxs-lookup"><span data-stu-id="00404-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="00404-205">ASP.NET Core 3.0 y versiones posteriores solo se ejecutan en .NET Core.</span><span class="sxs-lookup"><span data-stu-id="00404-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="00404-206">Para obtener más información sobre este cambio, vea [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Descripción general de los cambios que se aplicarán a ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="00404-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="00404-207">El uso de .NET Core como destino cuenta con varias ventajas que van en aumento con cada versión.</span><span class="sxs-lookup"><span data-stu-id="00404-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="00404-208">Entre las ventajas del uso de .NET Core en vez de .NET Framework se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="00404-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="00404-209">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="00404-209">Cross-platform.</span></span> <span data-ttu-id="00404-210">Ejecución en macOS, Linux y Windows.</span><span class="sxs-lookup"><span data-stu-id="00404-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="00404-211">Rendimiento mejorado</span><span class="sxs-lookup"><span data-stu-id="00404-211">Improved performance</span></span>
* [<span data-ttu-id="00404-212">Control de versiones en paralelo</span><span class="sxs-lookup"><span data-stu-id="00404-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="00404-213">Nuevas API.</span><span class="sxs-lookup"><span data-stu-id="00404-213">New APIs</span></span>
* <span data-ttu-id="00404-214">Código Abierto</span><span class="sxs-lookup"><span data-stu-id="00404-214">Open source</span></span>

<span data-ttu-id="00404-215">Para ayudar a cerrar la brecha de API existente entre .NET Framework y .NET Core, el [paquete de compatibilidad de Windows](/dotnet/core/porting/windows-compat-pack) ha permitido que miles de API solo de Windows estén disponibles en .NET Core.</span><span class="sxs-lookup"><span data-stu-id="00404-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="00404-216">Estas API no estaban disponibles en .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="00404-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="00404-217">Ruta de aprendizaje recomendada</span><span class="sxs-lookup"><span data-stu-id="00404-217">Recommended learning path</span></span>

<span data-ttu-id="00404-218">Se recomienda la siguiente secuencia de tutoriales y artículos para obtener una introducción para desarrollar aplicaciones de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="00404-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="00404-219">Realice el tutorial correspondiente al tipo de aplicación que quiera desarrollar o mantener.</span><span class="sxs-lookup"><span data-stu-id="00404-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="00404-220">Tipo de aplicación</span><span class="sxs-lookup"><span data-stu-id="00404-220">App type</span></span>  |<span data-ttu-id="00404-221">Escenario</span><span class="sxs-lookup"><span data-stu-id="00404-221">Scenario</span></span>  |<span data-ttu-id="00404-222">Tutorial</span><span class="sxs-lookup"><span data-stu-id="00404-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="00404-223">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="00404-223">Web app</span></span>                   | <span data-ttu-id="00404-224">Para un nuevo desarrollo</span><span class="sxs-lookup"><span data-stu-id="00404-224">For new development</span></span>        |[<span data-ttu-id="00404-225">Primeros pasos con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="00404-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="00404-226">Aplicación web</span><span class="sxs-lookup"><span data-stu-id="00404-226">Web app</span></span>                   | <span data-ttu-id="00404-227">Para mantener una aplicación MVC</span><span class="sxs-lookup"><span data-stu-id="00404-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="00404-228">Introducción a MVC</span><span class="sxs-lookup"><span data-stu-id="00404-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="00404-229">Web API</span><span class="sxs-lookup"><span data-stu-id="00404-229">Web API</span></span>                   |                            |<span data-ttu-id="00404-230">[Creación de una API web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="00404-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="00404-231">Aplicación en tiempo real</span><span class="sxs-lookup"><span data-stu-id="00404-231">Real-time app</span></span>             |                            |[<span data-ttu-id="00404-232">Introducción a SignalR</span><span class="sxs-lookup"><span data-stu-id="00404-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="00404-233">Realice un tutorial que muestra cómo realizar el acceso a datos básicos.</span><span class="sxs-lookup"><span data-stu-id="00404-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="00404-234">Escenario</span><span class="sxs-lookup"><span data-stu-id="00404-234">Scenario</span></span>  |<span data-ttu-id="00404-235">Tutorial</span><span class="sxs-lookup"><span data-stu-id="00404-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="00404-236">Para un nuevo desarrollo</span><span class="sxs-lookup"><span data-stu-id="00404-236">For new development</span></span>        |[<span data-ttu-id="00404-237">Razor Pages con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="00404-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="00404-238">Para mantener una aplicación MVC</span><span class="sxs-lookup"><span data-stu-id="00404-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="00404-239">MVC con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="00404-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="00404-240">Lea una introducción a los [conceptos básicos](xref:fundamentals/index) de ASP.NET Core aplicables a todos los tipos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="00404-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="00404-241">Examine la tabla de contenido para ver otros temas de interés.</span><span class="sxs-lookup"><span data-stu-id="00404-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="00404-242">&dagger;Hay también un [tutorial de API web que se realiza completamente en el explorador](/learn/modules/build-web-api-net-core); no es necesario instalar ningún IDE local.</span><span class="sxs-lookup"><span data-stu-id="00404-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="00404-243">El código se ejecuta en un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) y se usa [curl](https://curl.haxx.se/) para realizar pruebas.</span><span class="sxs-lookup"><span data-stu-id="00404-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="00404-244">Migración desde .NET Framework</span><span class="sxs-lookup"><span data-stu-id="00404-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="00404-245">Para obtener una guía de referencia para migrar aplicaciones de ASP.NET a ASP.NET Core, consulte <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="00404-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="00404-246">Cómo descargar un ejemplo</span><span class="sxs-lookup"><span data-stu-id="00404-246">How to download a sample</span></span>

<span data-ttu-id="00404-247">En muchos de los artículos y tutoriales se incluyen vínculos a código de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="00404-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="00404-248">[Descargue el archivo ZIP del repositorio de ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="00404-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="00404-249">Descomprima el archivo *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="00404-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="00404-250">Use la dirección URL del vínculo de ejemplo para ir al directorio de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="00404-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="00404-251">Directivas de preprocesador en código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="00404-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="00404-252">Para mostrar varios escenarios, las aplicaciones de ejemplo usan las directivas de preprocesador `#define` y `#if-#else/#elif-#endif` para compilar de forma selectiva y ejecutar secciones distintas de código de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="00404-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="00404-253">Para los ejemplos que usan este enfoque, establezca la directiva `#define` en la parte superior de los archivos C# para definir el símbolo asociado con el escenario que quiera ejecutar.</span><span class="sxs-lookup"><span data-stu-id="00404-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="00404-254">Algunos ejemplos requieren la definición del símbolo en la parte superior de varios archivos para ejecutar un escenario.</span><span class="sxs-lookup"><span data-stu-id="00404-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="00404-255">Por ejemplo, la siguiente lista de símbolos de `#define` indica que hay cuatro escenarios disponibles (un escenario por símbolo).</span><span class="sxs-lookup"><span data-stu-id="00404-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="00404-256">La configuración de ejemplo actual ejecuta el escenario `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="00404-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="00404-257">Para cambiar el ejemplo el escenario `ExpandDefault`, defina el símbolo `ExpandDefault` y deje los símbolos restantes comentados:</span><span class="sxs-lookup"><span data-stu-id="00404-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="00404-258">Para obtener información sobre cómo usar [directivas de preprocesador de C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar selectivamente secciones de código, vea [#define (Referencia de C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referencia de C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="00404-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="00404-259">Regiones en código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="00404-259">Regions in sample code</span></span>

<span data-ttu-id="00404-260">Algunas aplicaciones de ejemplo contienen secciones de código rodeadas de las directivas [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) y [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) de C#.</span><span class="sxs-lookup"><span data-stu-id="00404-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="00404-261">El sistema de creación de documentación inserta estas regiones en los temas de documentación representados.</span><span class="sxs-lookup"><span data-stu-id="00404-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="00404-262">Normalmente, los nombres de región contienen la palabra "snippet".</span><span class="sxs-lookup"><span data-stu-id="00404-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="00404-263">En el ejemplo siguiente se muestra una región denominada `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="00404-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="00404-264">En el archivo Markdown del tema se hace referencia al fragmento de código de C# anterior con la siguiente línea:</span><span class="sxs-lookup"><span data-stu-id="00404-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="00404-265">Puede ignorar sin problemas (o incluso quitar) las directivas `#region` y `#endregion` que rodean el código.</span><span class="sxs-lookup"><span data-stu-id="00404-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="00404-266">No altere el código de estas directivas si tiene planeado ejecutar los escenarios de ejemplo descritos en el tema.</span><span class="sxs-lookup"><span data-stu-id="00404-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="00404-267">Puede alterarlo si quiere experimentar con otros escenarios.</span><span class="sxs-lookup"><span data-stu-id="00404-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="00404-268">Para obtener más información, consulte [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets) (Contribución a la documentación de ASP.NET: fragmentos de código).</span><span class="sxs-lookup"><span data-stu-id="00404-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="00404-269">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="00404-269">Next steps</span></span>

<span data-ttu-id="00404-270">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="00404-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="00404-271">Conceptos básicos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00404-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="00404-272">[La reunión semanal de la comunidad de ASP.NET](https://live.asp.net/) trata el progreso y los planes del equipo.</span><span class="sxs-lookup"><span data-stu-id="00404-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="00404-273">Incluye nuevos blogs y nuevo software de terceros.</span><span class="sxs-lookup"><span data-stu-id="00404-273">It features new blogs and third-party software.</span></span>
