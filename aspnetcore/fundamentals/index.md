---
title: Conceptos básicos de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los conceptos básicos para crear aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 27b182394abe12a1631e5ba350942904bf4094aa
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035897"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="20c48-103">Conceptos básicos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20c48-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="20c48-104">En este artículo se proporciona una introducción de los temas clave para entender cómo desarrollar aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="20c48-105">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="20c48-105">The Startup class</span></span>

<span data-ttu-id="20c48-106">La clase `Startup` es donde:</span><span class="sxs-lookup"><span data-stu-id="20c48-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="20c48-107">Se configuran los servicios requeridos por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="20c48-108">La canalización de control de solicitudes de la aplicación se define como una serie de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="20c48-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="20c48-109">Aquí tiene una clase `Startup` de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20c48-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="20c48-110">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="20c48-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="20c48-111">Inserción de dependencias (servicios)</span><span class="sxs-lookup"><span data-stu-id="20c48-111">Dependency injection (services)</span></span>

<span data-ttu-id="20c48-112">ASP.NET Core incluye un marco de inserción de dependencias (DI) integrado que pone a disposición los servicios configurados a lo largo de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="20c48-113">Por ejemplo, un componente de registro es un servicio.</span><span class="sxs-lookup"><span data-stu-id="20c48-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="20c48-114">Se agrega al método `Startup.ConfigureServices` el código para configurar (o *registrar*) servicios.</span><span class="sxs-lookup"><span data-stu-id="20c48-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="20c48-115">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20c48-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="20c48-116">Los servicios se suelen resolver desde la inserción de dependencias mediante la inserción de constructores.</span><span class="sxs-lookup"><span data-stu-id="20c48-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="20c48-117">Con la inserción de constructores, una clase declara un parámetro de constructor del tipo requerido o una interfaz.</span><span class="sxs-lookup"><span data-stu-id="20c48-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="20c48-118">El marco de inserción de dependencias proporciona una instancia de este servicio en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="20c48-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="20c48-119">En el ejemplo siguiente se usa la inserción de constructores para resolver un `RazorPagesMovieContext` desde la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="20c48-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="20c48-120">Si el contenedor Inversión de control (IoC) integrado no satisface todas las necesidades de una aplicación, se puede usar en su lugar un contenedor de IoC de terceros.</span><span class="sxs-lookup"><span data-stu-id="20c48-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="20c48-121">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="20c48-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="20c48-122">Software intermedio</span><span class="sxs-lookup"><span data-stu-id="20c48-122">Middleware</span></span>

<span data-ttu-id="20c48-123">La canalización de control de solicitudes se compone de una serie de componentes de software intermedio.</span><span class="sxs-lookup"><span data-stu-id="20c48-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="20c48-124">Cada componente lleva a cabo las operaciones en un contexto `HttpContext` e invoca el middleware siguiente de la canalización, o bien finaliza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="20c48-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="20c48-125">Normalmente, se agrega un componente de software intermedio a la canalización al invocar un método de extensión `Use...` en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="20c48-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="20c48-126">Por ejemplo, para habilitar la representación de los archivos estáticos, llame a `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="20c48-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="20c48-127">En el ejemplo siguiente se configura una canalización de control de solicitudes:</span><span class="sxs-lookup"><span data-stu-id="20c48-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="20c48-128">ASP.NET Core incluye un amplio conjunto de middleware integrado.</span><span class="sxs-lookup"><span data-stu-id="20c48-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="20c48-129">También se pueden escribir componentes de middleware personalizados.</span><span class="sxs-lookup"><span data-stu-id="20c48-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="20c48-130">Para obtener más información, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="20c48-131">administrador de flujos de trabajo</span><span class="sxs-lookup"><span data-stu-id="20c48-131">Host</span></span>

<span data-ttu-id="20c48-132">En el inicio, una aplicación de ASP.NET Core compila un *host*.</span><span class="sxs-lookup"><span data-stu-id="20c48-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="20c48-133">El host encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="20c48-134">Una implementación de servidor de HTTP</span><span class="sxs-lookup"><span data-stu-id="20c48-134">An HTTP server implementation</span></span>
* <span data-ttu-id="20c48-135">Componentes de software intermedio</span><span class="sxs-lookup"><span data-stu-id="20c48-135">Middleware components</span></span>
* <span data-ttu-id="20c48-136">Registro</span><span class="sxs-lookup"><span data-stu-id="20c48-136">Logging</span></span>
* <span data-ttu-id="20c48-137">Servicios de inserción de dependencias (DI)</span><span class="sxs-lookup"><span data-stu-id="20c48-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="20c48-138">Configuración</span><span class="sxs-lookup"><span data-stu-id="20c48-138">Configuration</span></span>

<span data-ttu-id="20c48-139">Hay dos hosts distintos:</span><span class="sxs-lookup"><span data-stu-id="20c48-139">There are two different hosts:</span></span> 

* <span data-ttu-id="20c48-140">Host genérico de .NET</span><span class="sxs-lookup"><span data-stu-id="20c48-140">.NET Generic Host</span></span>
* <span data-ttu-id="20c48-141">Host web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20c48-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="20c48-142">Se recomienda el host genérico de .NET.</span><span class="sxs-lookup"><span data-stu-id="20c48-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="20c48-143">El host web de ASP.NET Core solo está disponible para la compatibilidad con versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="20c48-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="20c48-144">En el ejemplo siguiente se crea un host genérico de .NET:</span><span class="sxs-lookup"><span data-stu-id="20c48-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="20c48-145">Los métodos `CreateDefaultBuilder` y `ConfigureWebHostDefaults` configuran un host con un conjunto de opciones predeterminadas, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="20c48-146">Use [Kestrel](#servers) como servidor web y habilite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="20c48-147">Cargue la configuración de *appsettings.json* , *appsettings.{Environment Name}.json*, las variables de entorno, los argumentos de línea de comandos y otros orígenes de configuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="20c48-148">Envíe la salida de registro a la consola y los proveedores de depuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="20c48-149">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="20c48-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="20c48-150">Escenarios que no son web</span><span class="sxs-lookup"><span data-stu-id="20c48-150">Non-web scenarios</span></span>

<span data-ttu-id="20c48-151">El host genérico permite que otros tipos de aplicaciones usen las extensiones de marcos transversales, como el registro, la inserción de dependencias (DI), la configuración y la administración de la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="20c48-152">Para obtener más información, vea <xref:fundamentals/host/generic-host> y <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="20c48-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="20c48-153">Servidores</span><span class="sxs-lookup"><span data-stu-id="20c48-153">Servers</span></span>

<span data-ttu-id="20c48-154">Una aplicación ASP.NET Core usa una implementación de servidor HTTP para escuchar las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="20c48-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="20c48-155">El servidor expone las solicitudes a la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) integradas en un contexto `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="20c48-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="20c48-156">Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="20c48-157">ASP.NET Core proporciona las siguientes implementaciones de servidor:</span><span class="sxs-lookup"><span data-stu-id="20c48-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="20c48-158">*Kestrel* es un servidor web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="20c48-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="20c48-159">Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="20c48-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="20c48-160">En ASP.NET Core 2.0 y versiones posteriores, Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="20c48-161">*Servidor HTTP de IIS* es un servidor para Windows que usa IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="20c48-162">Con este servidor, la aplicación de ASP.NET Core e IIS se ejecutan en el mismo proceso.</span><span class="sxs-lookup"><span data-stu-id="20c48-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="20c48-163">*HTTP.sys* es un servidor de Windows que no se usa con IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="20c48-164">macOS</span><span class="sxs-lookup"><span data-stu-id="20c48-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="20c48-165">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-166">En ASP.NET Core 2.0 y versiones posteriores, Kestrel se puede ejecutar como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-167">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="20c48-168">Linux</span><span class="sxs-lookup"><span data-stu-id="20c48-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="20c48-169">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-170">En ASP.NET Core 2.0 y versiones posteriores, Kestrel se puede ejecutar como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-171">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="20c48-172">Para obtener más información, vea <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="20c48-173">Configuración</span><span class="sxs-lookup"><span data-stu-id="20c48-173">Configuration</span></span>

<span data-ttu-id="20c48-174">ASP.NET Core proporciona un marco de configuración que obtiene la configuración como pares nombre/valor de un conjunto ordenado de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="20c48-175">Hay disponibles proveedores de configuración integrados para una gran variedad de orígenes, como archivos *.json* y *.xml*, variables de entorno y argumentos de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="20c48-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="20c48-176">Escriba proveedores de configuración personalizados para admitir otros orígenes.</span><span class="sxs-lookup"><span data-stu-id="20c48-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="20c48-177">[De manera predeterminada](xref:fundamentals/configuration/index#default), las aplicaciones de ASP.NET Core están configuradas para leer desde *appsettings.json* , variables de entorno, la línea de comandos, etc.</span><span class="sxs-lookup"><span data-stu-id="20c48-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="20c48-178">Cuando se carga la configuración de la aplicación, los valores de las variables de entorno invalidan los valores de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="20c48-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="20c48-179">La mejor manera de leer valores de configuración relacionados es usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="20c48-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="20c48-180">Para más información, consulte [Enlace de datos de configuración jerárquica mediante el patrón de opciones](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="20c48-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="20c48-181">Para administrar los datos de configuración confidencial como las contraseñas, NET Core proporciona el [Administrador de secretos](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="20c48-181">For managing confidential configuration data such as passwords, .NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="20c48-182">Para los secretos de producción, se recomienda [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="20c48-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="20c48-183">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="20c48-184">Entornos</span><span class="sxs-lookup"><span data-stu-id="20c48-184">Environments</span></span>

<span data-ttu-id="20c48-185">Los entornos de ejecución, como `Development`, `Staging` y `Production`, son un concepto de primera clase en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="20c48-186">Especifique el entorno que ejecuta una aplicación estableciendo la variable de entorno `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="20c48-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="20c48-187">ASP.NET Core lee dicha variable de entorno al inicio de la aplicación y almacena el valor en una implementación `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="20c48-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="20c48-188">Esta implementación está disponible en cualquier parte de una aplicación a través de la inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="20c48-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="20c48-189">En el ejemplo siguiente se configura la aplicación para proporcionar información detallada del error cuando se ejecuta en el entorno de `Development`:</span><span class="sxs-lookup"><span data-stu-id="20c48-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="20c48-190">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="20c48-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="20c48-191">Registro</span><span class="sxs-lookup"><span data-stu-id="20c48-191">Logging</span></span>

<span data-ttu-id="20c48-192">ASP.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="20c48-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="20c48-193">Los proveedores disponibles incluyen:</span><span class="sxs-lookup"><span data-stu-id="20c48-193">Available providers include:</span></span>

* <span data-ttu-id="20c48-194">Consola</span><span class="sxs-lookup"><span data-stu-id="20c48-194">Console</span></span>
* <span data-ttu-id="20c48-195">Depuración</span><span class="sxs-lookup"><span data-stu-id="20c48-195">Debug</span></span>
* <span data-ttu-id="20c48-196">Seguimiento de eventos en Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="20c48-197">Registro de errores de Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-197">Windows Event Log</span></span>
* <span data-ttu-id="20c48-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="20c48-198">TraceSource</span></span>
* <span data-ttu-id="20c48-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20c48-199">Azure App Service</span></span>
* <span data-ttu-id="20c48-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="20c48-200">Azure Application Insights</span></span>

<span data-ttu-id="20c48-201">Para crear registros, resuelva un servicio <xref:Microsoft.Extensions.Logging.ILogger%601> a partir de la inserción de dependencias (DI) y llame a métodos de registro como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span><span class="sxs-lookup"><span data-stu-id="20c48-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="20c48-202">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20c48-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="20c48-203">Los métodos de registro como `LogInformation` admiten cualquier número de campos.</span><span class="sxs-lookup"><span data-stu-id="20c48-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="20c48-204">Por lo general, estos campos se utilizan para construir un mensaje `string`, pero algunos proveedores de registro los envían a un almacén de datos como campos independientes.</span><span class="sxs-lookup"><span data-stu-id="20c48-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="20c48-205">Esta característica permite a los proveedores de registro implementar el [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="20c48-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="20c48-206">Para obtener más información, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="20c48-207">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="20c48-207">Routing</span></span>

<span data-ttu-id="20c48-208">Una *ruta* es un patrón de dirección URL que se asigna a un controlador.</span><span class="sxs-lookup"><span data-stu-id="20c48-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="20c48-209">El controlador normalmente es una página de Razor, un método de acción en un controlador MVC o un software intermedio.</span><span class="sxs-lookup"><span data-stu-id="20c48-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="20c48-210">El enrutamiento de ASP.NET Core le permite controlar las direcciones URL usadas por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="20c48-211">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="20c48-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="20c48-212">Control de errores</span><span class="sxs-lookup"><span data-stu-id="20c48-212">Error handling</span></span>

<span data-ttu-id="20c48-213">ASP.NET Core tiene características integradas para controlar los errores, tales como:</span><span class="sxs-lookup"><span data-stu-id="20c48-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="20c48-214">Una página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="20c48-214">A developer exception page</span></span>
* <span data-ttu-id="20c48-215">Páginas de errores personalizados</span><span class="sxs-lookup"><span data-stu-id="20c48-215">Custom error pages</span></span>
* <span data-ttu-id="20c48-216">Páginas de códigos de estado estáticos</span><span class="sxs-lookup"><span data-stu-id="20c48-216">Static status code pages</span></span>
* <span data-ttu-id="20c48-217">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="20c48-217">Startup exception handling</span></span>

<span data-ttu-id="20c48-218">Para obtener más información, vea <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="20c48-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="20c48-219">Realización de solicitudes HTTP</span><span class="sxs-lookup"><span data-stu-id="20c48-219">Make HTTP requests</span></span>

<span data-ttu-id="20c48-220">Una implementación de `IHttpClientFactory` está disponible para crear instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="20c48-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="20c48-221">Este servicio:</span><span class="sxs-lookup"><span data-stu-id="20c48-221">The factory:</span></span>

* <span data-ttu-id="20c48-222">Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas.</span><span class="sxs-lookup"><span data-stu-id="20c48-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="20c48-223">Por ejemplo, registre y configure un cliente de *github* para acceder a GitHub.</span><span class="sxs-lookup"><span data-stu-id="20c48-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="20c48-224">Registre y configure un cliente predeterminado para otros fines.</span><span class="sxs-lookup"><span data-stu-id="20c48-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="20c48-225">Admite el registro y encadenamiento de varios controladores de delegación para crear una canalización de middleware de solicitud saliente.</span><span class="sxs-lookup"><span data-stu-id="20c48-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="20c48-226">Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="20c48-227">Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.</span><span class="sxs-lookup"><span data-stu-id="20c48-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="20c48-228">Se integra con *Polly*, una conocida biblioteca de terceros para el control de errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="20c48-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="20c48-229">Administra la agrupación y duración de las instancias de `HttpClientHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar manualmente las duraciones de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="20c48-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="20c48-230">Agrega una experiencia de registro configurable a través de <xref:Microsoft.Extensions.Logging.ILogger> en todas las solicitudes enviadas a través de los clientes creados por Factory.</span><span class="sxs-lookup"><span data-stu-id="20c48-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="20c48-231">Para obtener más información, vea <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="20c48-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="20c48-232">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="20c48-232">Content root</span></span>

<span data-ttu-id="20c48-233">La raíz del contenido es la ruta de acceso base para:</span><span class="sxs-lookup"><span data-stu-id="20c48-233">The content root is the base path for:</span></span>

* <span data-ttu-id="20c48-234">El archivo ejecutable que hospeda la aplicación ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="20c48-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="20c48-235">Los ensamblados compilados que componen la aplicación ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="20c48-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="20c48-236">Los archivos de contenido usados por la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="20c48-237">Archivos de Razor ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="20c48-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="20c48-238">Archivos de configuración ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="20c48-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="20c48-239">Archivos de datos ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="20c48-239">Data files (*.db*)</span></span>
* <span data-ttu-id="20c48-240">La [raíz web](#web-root), por lo general la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="20c48-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="20c48-241">Durante el desarrollo, la raíz del contenido tiene como valor predeterminado el directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="20c48-242">Este directorio también es la ruta de acceso base para los archivos de contenido de la aplicación y la [raíz web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="20c48-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="20c48-243">Especifique una raíz de contenido diferente estableciendo su ruta de acceso al [compilar el host](#host).</span><span class="sxs-lookup"><span data-stu-id="20c48-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="20c48-244">Para obtener más información, vea [Raíz del contenido](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="20c48-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="20c48-245">Raíz web</span><span class="sxs-lookup"><span data-stu-id="20c48-245">Web root</span></span>

<span data-ttu-id="20c48-246">La raíz web es la ruta de acceso base para los archivos de recursos estáticos públicos, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="20c48-247">Hojas de estilo ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="20c48-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="20c48-248">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="20c48-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="20c48-249">Imágenes ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="20c48-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="20c48-250">De manera predeterminada, los archivos estáticos solo se sirven desde el directorio raíz web y sus subdirectorios.</span><span class="sxs-lookup"><span data-stu-id="20c48-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="20c48-251">La ruta de acceso raíz web se establece de manera predeterminada en *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="20c48-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="20c48-252">Para especificar otra raíz web, establezca su ruta de acceso al [compilar el host](#host).</span><span class="sxs-lookup"><span data-stu-id="20c48-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="20c48-253">Para obtener más información, vea [Raíz web](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="20c48-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="20c48-254">Evite la publicación de archivos en *wwwroot* con el elemento del proyecto [\<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="20c48-255">En el ejemplo siguiente se impide la publicación de contenido en *wwwroot/local* y sus subdirectorios:</span><span class="sxs-lookup"><span data-stu-id="20c48-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="20c48-256">En los archivos Razor *.cshtml*, la virgulilla (`~/`) apunta a la raíz web.</span><span class="sxs-lookup"><span data-stu-id="20c48-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="20c48-257">Una ruta de acceso que empieza por `~/` se conoce como *ruta de acceso virtual*.</span><span class="sxs-lookup"><span data-stu-id="20c48-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="20c48-258">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="20c48-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="20c48-259">Este artículo es una introducción de los temas clave para entender cómo desarrollar aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="20c48-260">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="20c48-260">The Startup class</span></span>

<span data-ttu-id="20c48-261">La clase `Startup` es donde:</span><span class="sxs-lookup"><span data-stu-id="20c48-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="20c48-262">Se configuran los servicios requeridos por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="20c48-263">Se define la solicitud de canalización.</span><span class="sxs-lookup"><span data-stu-id="20c48-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="20c48-264">Los *servicios* son componentes que usan la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="20c48-265">Por ejemplo, un componente de registro es un servicio.</span><span class="sxs-lookup"><span data-stu-id="20c48-265">For example, a logging component is a service.</span></span> <span data-ttu-id="20c48-266">Se agrega al método `Startup.ConfigureServices` el código para configurar (o *registrar*) servicios.</span><span class="sxs-lookup"><span data-stu-id="20c48-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="20c48-267">La canalización de control de solicitudes se compone de una serie de *componentes de software intermedio*.</span><span class="sxs-lookup"><span data-stu-id="20c48-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="20c48-268">Por ejemplo, un software intermedio podría controlar las solicitudes de archivos estáticos o redirigir las solicitudes HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="20c48-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="20c48-269">Cada software intermedio lleva a cabo las operaciones asincrónicas en un contexto `HttpContext` y, después, invoca el siguiente software intermedio de la canalización o finaliza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="20c48-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="20c48-270">Se agrega al método `Startup.Configure` el código para configurar la canalización de control de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="20c48-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="20c48-271">Aquí tiene una clase `Startup` de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20c48-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="20c48-272">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="20c48-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="20c48-273">Inserción de dependencias (servicios)</span><span class="sxs-lookup"><span data-stu-id="20c48-273">Dependency injection (services)</span></span>

<span data-ttu-id="20c48-274">ASP.NET Core tiene un marco de inserción de dependencias (DI) integrado que pone a disposición los servicios configurados para las clases de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="20c48-275">Una manera de obtener una instancia de un servicio en una clase es crear un constructor con un parámetro del tipo necesario.</span><span class="sxs-lookup"><span data-stu-id="20c48-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="20c48-276">El parámetro puede ser el tipo de servicio o una interfaz.</span><span class="sxs-lookup"><span data-stu-id="20c48-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="20c48-277">El sistema de DI proporciona el servicio en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="20c48-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="20c48-278">Aquí tiene una clase que usa DI para obtener un objeto de contexto de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="20c48-279">La línea resaltada es un ejemplo de inserción de constructor:</span><span class="sxs-lookup"><span data-stu-id="20c48-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="20c48-280">Aunque DI está integrada, está diseñada para permitirle conectar un contenedor de inversión de control (IoC) de terceros si lo prefiere.</span><span class="sxs-lookup"><span data-stu-id="20c48-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="20c48-281">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="20c48-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="20c48-282">Software intermedio</span><span class="sxs-lookup"><span data-stu-id="20c48-282">Middleware</span></span>

<span data-ttu-id="20c48-283">La canalización de control de solicitudes se compone de una serie de componentes de software intermedio.</span><span class="sxs-lookup"><span data-stu-id="20c48-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="20c48-284">Cada componente lleva a cabo las operaciones asincrónicas en un contexto `HttpContext` y, después, invoca el siguiente software intermedio de la canalización o finaliza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="20c48-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="20c48-285">Normalmente, se agrega un componente de software intermedio a la canalización al invocar su método de extensión `Use...` en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="20c48-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="20c48-286">Por ejemplo, para habilitar la representación de los archivos estáticos, llame a `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="20c48-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="20c48-287">El código resaltado en el ejemplo siguiente configura la canalización de control de solicitudes:</span><span class="sxs-lookup"><span data-stu-id="20c48-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="20c48-288">ASP.NET Core incluye una gran variedad de software intermedio integrado. Además, puede escribir software intermedio personalizado.</span><span class="sxs-lookup"><span data-stu-id="20c48-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="20c48-289">Para obtener más información, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="20c48-290">administrador de flujos de trabajo</span><span class="sxs-lookup"><span data-stu-id="20c48-290">Host</span></span>

<span data-ttu-id="20c48-291">Una aplicación de ASP.NET Core compila un *host* durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="20c48-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="20c48-292">El host es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="20c48-293">Una implementación de servidor de HTTP</span><span class="sxs-lookup"><span data-stu-id="20c48-293">An HTTP server implementation</span></span>
* <span data-ttu-id="20c48-294">Componentes de software intermedio</span><span class="sxs-lookup"><span data-stu-id="20c48-294">Middleware components</span></span>
* <span data-ttu-id="20c48-295">Registro</span><span class="sxs-lookup"><span data-stu-id="20c48-295">Logging</span></span>
* <span data-ttu-id="20c48-296">DI</span><span class="sxs-lookup"><span data-stu-id="20c48-296">DI</span></span>
* <span data-ttu-id="20c48-297">Configuración</span><span class="sxs-lookup"><span data-stu-id="20c48-297">Configuration</span></span>

<span data-ttu-id="20c48-298">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="20c48-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="20c48-299">Hay dos hosts disponibles: el host web y el host genérico.</span><span class="sxs-lookup"><span data-stu-id="20c48-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="20c48-300">En ASP.NET Core 2.x, el host genérico es solo para escenarios que no son web.</span><span class="sxs-lookup"><span data-stu-id="20c48-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="20c48-301">El código para crear un host está en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="20c48-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="20c48-302">El métodos `CreateDefaultBuilder` configura un host con opciones de uso común, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="20c48-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="20c48-303">Use [Kestrel](#servers) como servidor web y habilite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="20c48-304">Cargue la configuración de *appsettings.json* , *appsettings.{Environment Name}.json*, las variables de entorno, los argumentos de línea de comandos y otros orígenes de configuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="20c48-305">Envíe la salida de registro a la consola y los proveedores de depuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="20c48-306">Para obtener más información, vea <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="20c48-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="20c48-307">Escenarios que no son web</span><span class="sxs-lookup"><span data-stu-id="20c48-307">Non-web scenarios</span></span>

<span data-ttu-id="20c48-308">El host genérico permite que otros tipos de aplicaciones usen las extensiones de marcos transversales, como el registro, la inserción de dependencias (DI), la configuración y la administración de la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="20c48-309">Para obtener más información, vea <xref:fundamentals/host/generic-host> y <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="20c48-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="20c48-310">Servidores</span><span class="sxs-lookup"><span data-stu-id="20c48-310">Servers</span></span>

<span data-ttu-id="20c48-311">Una aplicación ASP.NET Core usa una implementación de servidor HTTP para escuchar las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="20c48-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="20c48-312">El servidor expone las solicitudes a la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) integradas en un contexto `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="20c48-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="20c48-313">Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="20c48-314">ASP.NET Core proporciona las siguientes implementaciones de servidor:</span><span class="sxs-lookup"><span data-stu-id="20c48-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="20c48-315">*Kestrel* es un servidor web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="20c48-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="20c48-316">Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="20c48-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="20c48-317">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="20c48-318">Un *servidor HTTP de IIS* es un servidor para Windows que usa IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="20c48-319">Con este servidor, la aplicación de ASP.NET Core e IIS se ejecutan en el mismo proceso.</span><span class="sxs-lookup"><span data-stu-id="20c48-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="20c48-320">*HTTP.sys* es un servidor de Windows que no se usa con IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="20c48-321">macOS</span><span class="sxs-lookup"><span data-stu-id="20c48-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="20c48-322">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-323">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-324">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="20c48-325">Linux</span><span class="sxs-lookup"><span data-stu-id="20c48-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="20c48-326">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-327">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-328">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="20c48-329">Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="20c48-330">ASP.NET Core proporciona las siguientes implementaciones de servidor:</span><span class="sxs-lookup"><span data-stu-id="20c48-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="20c48-331">*Kestrel* es un servidor web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="20c48-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="20c48-332">Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="20c48-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="20c48-333">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="20c48-334">*HTTP.sys* es un servidor de Windows que no se usa con IIS.</span><span class="sxs-lookup"><span data-stu-id="20c48-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="20c48-335">macOS</span><span class="sxs-lookup"><span data-stu-id="20c48-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="20c48-336">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-337">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-338">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="20c48-339">Linux</span><span class="sxs-lookup"><span data-stu-id="20c48-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="20c48-340">ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="20c48-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="20c48-341">Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.</span><span class="sxs-lookup"><span data-stu-id="20c48-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="20c48-342">Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="20c48-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="20c48-343">Para obtener más información, vea <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="20c48-344">Configuración</span><span class="sxs-lookup"><span data-stu-id="20c48-344">Configuration</span></span>

<span data-ttu-id="20c48-345">ASP.NET Core proporciona un marco de configuración que obtiene la configuración como pares nombre/valor de un conjunto ordenado de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="20c48-346">Hay proveedores de configuración integrados para una gran variedad de orígenes, como archivos *.json* y *.xml*, variables de entorno y argumentos de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="20c48-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="20c48-347">También puede escribir proveedores de configuración personalizados.</span><span class="sxs-lookup"><span data-stu-id="20c48-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="20c48-348">Por ejemplo, puede especificar que la configuración procede de *appsettings.json* y las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="20c48-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="20c48-349">Después, cuando se solicite el valor de *ConnectionString*, el marco buscará primero en el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="20c48-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="20c48-350">Si se encuentra el valor allí, pero también en una variable de entorno, el valor de la variable de entorno tendría prioridad.</span><span class="sxs-lookup"><span data-stu-id="20c48-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="20c48-351">Para administrar los datos de configuración confidencial como las contraseñas, .NET Core proporciona una [herramienta Administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="20c48-351">For managing confidential configuration data such as passwords, .NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="20c48-352">Para los secretos de producción, se recomienda [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="20c48-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="20c48-353">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="20c48-354">Opciones</span><span class="sxs-lookup"><span data-stu-id="20c48-354">Options</span></span>

<span data-ttu-id="20c48-355">Siempre que sea posible, ASP.NET Core sigue el *patrón de opciones* para almacenar y recuperar los valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="20c48-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="20c48-356">El patrón de opciones usa clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="20c48-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="20c48-357">Por ejemplo, el código siguiente define las opciones de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="20c48-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="20c48-358">Para obtener más información, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="20c48-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="20c48-359">Entornos</span><span class="sxs-lookup"><span data-stu-id="20c48-359">Environments</span></span>

<span data-ttu-id="20c48-360">Los entornos de ejecución, como *desarrollo*, *almacenamiento provisional* y *Producción*, son un concepto de primera clase en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="20c48-361">Puede especificar el entorno que ejecuta una aplicación estableciendo la variable de entorno `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="20c48-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="20c48-362">ASP.NET Core lee dicha variable de entorno al inicio de la aplicación y almacena el valor en una implementación `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="20c48-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="20c48-363">El objeto de entorno está disponible en cualquier parte de la aplicación a través de DI.</span><span class="sxs-lookup"><span data-stu-id="20c48-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="20c48-364">El siguiente ejemplo de código desde la clase `Startup` configura la aplicación para que proporcione información detallada del error solo cuando se ejecuta en el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="20c48-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="20c48-365">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="20c48-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="20c48-366">Registro</span><span class="sxs-lookup"><span data-stu-id="20c48-366">Logging</span></span>

<span data-ttu-id="20c48-367">ASP.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros.</span><span class="sxs-lookup"><span data-stu-id="20c48-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="20c48-368">Entre los proveedores disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="20c48-368">Available providers include the following:</span></span>

* <span data-ttu-id="20c48-369">Consola</span><span class="sxs-lookup"><span data-stu-id="20c48-369">Console</span></span>
* <span data-ttu-id="20c48-370">Depuración</span><span class="sxs-lookup"><span data-stu-id="20c48-370">Debug</span></span>
* <span data-ttu-id="20c48-371">Seguimiento de eventos en Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="20c48-372">Registro de errores de Windows</span><span class="sxs-lookup"><span data-stu-id="20c48-372">Windows Event Log</span></span>
* <span data-ttu-id="20c48-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="20c48-373">TraceSource</span></span>
* <span data-ttu-id="20c48-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20c48-374">Azure App Service</span></span>
* <span data-ttu-id="20c48-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="20c48-375">Azure Application Insights</span></span>

<span data-ttu-id="20c48-376">Escribir registros desde cualquier lugar en el código de una aplicación mediante la obtención de un objeto `ILogger` de DI y llamar a métodos de registro.</span><span class="sxs-lookup"><span data-stu-id="20c48-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="20c48-377">Aquí tiene un código de ejemplo que usa un objeto `ILogger`, con la inserción del constructor y resaltadas las llamadas del método de registro.</span><span class="sxs-lookup"><span data-stu-id="20c48-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="20c48-378">La interfaz `ILogger` le permite pasar cualquier número de campos para el proveedor de registro.</span><span class="sxs-lookup"><span data-stu-id="20c48-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="20c48-379">Los campos habitualmente se usan para construir una cadena de mensaje, pero el proveedor también puede enviarlos como campos independientes o almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="20c48-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="20c48-380">Esta característica permite a los proveedores de registro implementar el [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="20c48-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="20c48-381">Para obtener más información, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="20c48-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="20c48-382">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="20c48-382">Routing</span></span>

<span data-ttu-id="20c48-383">Una *ruta* es un patrón de dirección URL que se asigna a un controlador.</span><span class="sxs-lookup"><span data-stu-id="20c48-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="20c48-384">El controlador normalmente es una página de Razor, un método de acción en un controlador MVC o un software intermedio.</span><span class="sxs-lookup"><span data-stu-id="20c48-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="20c48-385">El enrutamiento de ASP.NET Core le permite controlar las direcciones URL usadas por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20c48-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="20c48-386">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="20c48-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="20c48-387">Control de errores</span><span class="sxs-lookup"><span data-stu-id="20c48-387">Error handling</span></span>

<span data-ttu-id="20c48-388">ASP.NET Core tiene características integradas para controlar los errores, tales como:</span><span class="sxs-lookup"><span data-stu-id="20c48-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="20c48-389">Una página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="20c48-389">A developer exception page</span></span>
* <span data-ttu-id="20c48-390">Páginas de errores personalizados</span><span class="sxs-lookup"><span data-stu-id="20c48-390">Custom error pages</span></span>
* <span data-ttu-id="20c48-391">Páginas de códigos de estado estáticos</span><span class="sxs-lookup"><span data-stu-id="20c48-391">Static status code pages</span></span>
* <span data-ttu-id="20c48-392">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="20c48-392">Startup exception handling</span></span>

<span data-ttu-id="20c48-393">Para obtener más información, vea <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="20c48-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="20c48-394">Realización de solicitudes HTTP</span><span class="sxs-lookup"><span data-stu-id="20c48-394">Make HTTP requests</span></span>

<span data-ttu-id="20c48-395">Una implementación de `IHttpClientFactory` está disponible para crear instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="20c48-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="20c48-396">Este servicio:</span><span class="sxs-lookup"><span data-stu-id="20c48-396">The factory:</span></span>

* <span data-ttu-id="20c48-397">Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas.</span><span class="sxs-lookup"><span data-stu-id="20c48-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="20c48-398">Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a GitHub.</span><span class="sxs-lookup"><span data-stu-id="20c48-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="20c48-399">y, de igual modo, registrar otro cliente predeterminado para otros fines.</span><span class="sxs-lookup"><span data-stu-id="20c48-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="20c48-400">Admite el registro y encadenamiento de varios controladores de delegación para crear una canalización de middleware de solicitud saliente.</span><span class="sxs-lookup"><span data-stu-id="20c48-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="20c48-401">Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20c48-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="20c48-402">Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.</span><span class="sxs-lookup"><span data-stu-id="20c48-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="20c48-403">Se integra con *Polly*, una conocida biblioteca de terceros para el control de errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="20c48-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="20c48-404">Administra la agrupación y duración de las instancias de `HttpClientHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.</span><span class="sxs-lookup"><span data-stu-id="20c48-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="20c48-405">Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.</span><span class="sxs-lookup"><span data-stu-id="20c48-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="20c48-406">Para obtener más información, vea <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="20c48-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="20c48-407">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="20c48-407">Content root</span></span>

<span data-ttu-id="20c48-408">La raíz del contenido es la ruta de acceso base a:</span><span class="sxs-lookup"><span data-stu-id="20c48-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="20c48-409">El archivo ejecutable que hospeda la aplicación ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="20c48-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="20c48-410">Los ensamblados compilados que componen la aplicación ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="20c48-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="20c48-411">Los archivos de contenido que no son de código usados por la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="20c48-412">Archivos de Razor ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="20c48-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="20c48-413">Archivos de configuración ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="20c48-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="20c48-414">Archivos de datos ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="20c48-414">Data files (*.db*)</span></span>
* <span data-ttu-id="20c48-415">[Raíz web](#web-root), normalmente la carpeta *wwwroot* publicada.</span><span class="sxs-lookup"><span data-stu-id="20c48-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="20c48-416">Durante el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="20c48-416">During development:</span></span>

* <span data-ttu-id="20c48-417">La raíz del contenido tiene como valor predeterminado el directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="20c48-418">El directorio raíz del proyecto se usa para crear:</span><span class="sxs-lookup"><span data-stu-id="20c48-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="20c48-419">La ruta de acceso a los archivos de contenido que no son de código de la aplicación en el directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="20c48-420">La [raíz web](#web-root), normalmente la carpeta *wwwroot* en el directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="20c48-421">Se puede especificar una ruta raíz de contenido alternativa al [crear el host](#host).</span><span class="sxs-lookup"><span data-stu-id="20c48-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="20c48-422">Para obtener más información, vea <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="20c48-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="20c48-423">Raíz web</span><span class="sxs-lookup"><span data-stu-id="20c48-423">Web root</span></span>

<span data-ttu-id="20c48-424">La raíz web es la ruta de acceso base a archivos de recursos públicos, que no son de código y estáticos, como:</span><span class="sxs-lookup"><span data-stu-id="20c48-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="20c48-425">Hojas de estilo ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="20c48-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="20c48-426">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="20c48-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="20c48-427">Imágenes ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="20c48-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="20c48-428">De forma predeterminada, los archivos estáticos se atienden solo desde el directorio raíz web (y los subdirectorios).</span><span class="sxs-lookup"><span data-stu-id="20c48-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="20c48-429">El valor predeterminado de la ruta de acceso de la raíz web es *{raíz del contenido}/wwwroot*, pero se puede especificar una raíz web diferente [al crear el host](#host).</span><span class="sxs-lookup"><span data-stu-id="20c48-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="20c48-430">Para obtener más información, vea [Raíz web](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="20c48-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="20c48-431">Evite la publicación de archivos en *wwwroot* con el elemento del proyecto [\<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) del archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="20c48-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="20c48-432">En el ejemplo siguiente se impide la publicación de contenido en el directorio *wwwroot/local* y en los subdirectorios:</span><span class="sxs-lookup"><span data-stu-id="20c48-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="20c48-433">En los archivos de Razor ( *.cshtml*), la virgulilla `~/` apunta a la raíz web.</span><span class="sxs-lookup"><span data-stu-id="20c48-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="20c48-434">Una ruta de acceso que empieza por `~/` se conoce como *ruta de acceso virtual*.</span><span class="sxs-lookup"><span data-stu-id="20c48-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="20c48-435">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="20c48-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
