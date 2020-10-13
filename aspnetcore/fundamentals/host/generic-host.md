---
title: Host genérico de .NET en ASP.NET Core
author: rick-anderson
description: Use el host genérico de .NET Core en las aplicaciones de ASP.NET Core.  El host genérico es responsable de la administración del inicio y la duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762326"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="d098f-104">Host genérico de .NET en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d098f-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d098f-105">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="d098f-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d098f-106">En este tema se proporciona información sobre cómo usar el host genérico de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d098f-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d098f-107">Para obtener información sobre cómo usar un host genérico de .NET en aplicaciones de consola, consulte [Host genérico de .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d098f-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d098f-108">Definición de host</span><span class="sxs-lookup"><span data-stu-id="d098f-108">Host definition</span></span>

<span data-ttu-id="d098f-109">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="d098f-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d098f-110">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="d098f-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="d098f-111">Registro</span><span class="sxs-lookup"><span data-stu-id="d098f-111">Logging</span></span>
* <span data-ttu-id="d098f-112">Configuración</span><span class="sxs-lookup"><span data-stu-id="d098f-112">Configuration</span></span>
* <span data-ttu-id="d098f-113">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d098f-113">`IHostedService` implementations</span></span>

<span data-ttu-id="d098f-114">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d098f-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d098f-115">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d098f-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d098f-116">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d098f-117">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="d098f-117">Set up a host</span></span>

<span data-ttu-id="d098f-118">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="d098f-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d098f-119">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-119">The `Main` method:</span></span>

* <span data-ttu-id="d098f-120">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d098f-121">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d098f-122">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host:</span><span class="sxs-lookup"><span data-stu-id="d098f-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d098f-123">En el código siguiente se crea una carga de trabajo que no es HTTP con una implementación de `IHostedService` agregada al contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="d098f-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d098f-124">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="d098f-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d098f-125">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d098f-126">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d098f-127">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d098f-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d098f-128">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="d098f-128">Default builder settings</span></span>

<span data-ttu-id="d098f-129">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d098f-130">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d098f-131">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="d098f-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="d098f-132">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d098f-133">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d098f-133">Command-line arguments.</span></span>
* <span data-ttu-id="d098f-134">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="d098f-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="d098f-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="d098f-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d098f-137">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="d098f-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d098f-138">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-138">Environment variables.</span></span>
  * <span data-ttu-id="d098f-139">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d098f-139">Command-line arguments.</span></span>
* <span data-ttu-id="d098f-140">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="d098f-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d098f-141">Consola</span><span class="sxs-lookup"><span data-stu-id="d098f-141">Console</span></span>
  * <span data-ttu-id="d098f-142">Depuración</span><span class="sxs-lookup"><span data-stu-id="d098f-142">Debug</span></span>
  * <span data-ttu-id="d098f-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="d098f-143">EventSource</span></span>
  * <span data-ttu-id="d098f-144">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="d098f-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d098f-145">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d098f-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d098f-146">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d098f-147">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d098f-148">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d098f-149">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d098f-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d098f-150">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d098f-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d098f-151">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="d098f-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d098f-152">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="d098f-152">Enables IIS integration.</span></span> <span data-ttu-id="d098f-153">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d098f-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d098f-154">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d098f-155">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="d098f-155">Framework-provided services</span></span>

<span data-ttu-id="d098f-156">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="d098f-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d098f-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d098f-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d098f-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d098f-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d098f-160">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="d098f-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d098f-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="d098f-162">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d098f-163">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="d098f-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d098f-164">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d098f-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d098f-165">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d098f-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d098f-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-166">IHostLifetime</span></span>

<span data-ttu-id="d098f-167">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="d098f-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d098f-168">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="d098f-168">The last implementation registered is used.</span></span>

<span data-ttu-id="d098f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="d098f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d098f-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d098f-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d098f-171">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d098f-172">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d098f-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d098f-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d098f-173">IHostEnvironment</span></span>

<span data-ttu-id="d098f-174">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d098f-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d098f-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d098f-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d098f-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d098f-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d098f-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d098f-178">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d098f-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d098f-179">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="d098f-179">Host configuration</span></span>

<span data-ttu-id="d098f-180">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d098f-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d098f-181">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d098f-182">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d098f-183">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d098f-184">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-185">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d098f-186">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d098f-187">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d098f-188">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d098f-189">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="d098f-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d098f-190">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="d098f-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d098f-191">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="d098f-191">App configuration</span></span>

<span data-ttu-id="d098f-192">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d098f-193">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-194">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d098f-195">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="d098f-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d098f-196">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d098f-197">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d098f-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d098f-198">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="d098f-198">Settings for all app types</span></span>

<span data-ttu-id="d098f-199">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d098f-200">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d098f-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d098f-201">ApplicationName</span></span>

<span data-ttu-id="d098f-202">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="d098f-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d098f-203">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d098f-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d098f-204">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-204">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-205">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d098f-206">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d098f-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d098f-207">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d098f-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-208">ContentRoot</span></span>

<span data-ttu-id="d098f-209">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="d098f-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d098f-210">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="d098f-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d098f-211">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d098f-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d098f-212">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-212">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-213">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d098f-214">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d098f-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d098f-215">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d098f-216">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d098f-216">For more information, see:</span></span>

* [<span data-ttu-id="d098f-217">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="d098f-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d098f-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d098f-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d098f-219">EnvironmentName</span></span>

<span data-ttu-id="d098f-220">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="d098f-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d098f-221">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="d098f-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d098f-222">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d098f-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d098f-223">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d098f-223">**Key**: `environment`</span></span>  
<span data-ttu-id="d098f-224">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-224">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-225">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d098f-225">**Default**: `Production`</span></span>  
<span data-ttu-id="d098f-226">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d098f-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d098f-227">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d098f-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d098f-228">ShutdownTimeout</span></span>

<span data-ttu-id="d098f-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d098f-230">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="d098f-230">The default value is five seconds.</span></span>  <span data-ttu-id="d098f-231">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="d098f-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="d098f-232">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d098f-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d098f-233">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="d098f-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d098f-234">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d098f-235">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="d098f-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d098f-236">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d098f-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d098f-237">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d098f-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d098f-238">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="d098f-238">**Type**: `int`</span></span>  
<span data-ttu-id="d098f-239">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="d098f-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d098f-240">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d098f-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d098f-241">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d098f-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d098f-242">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="d098f-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="d098f-243">Deshabilitación de la recarga de configuración de aplicaciones al realizar un cambio</span><span class="sxs-lookup"><span data-stu-id="d098f-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="d098f-244">[De forma predeterminada](xref:fundamentals/configuration/index#default), *appsettings.json* y *appsettings.{Entorno}.json* se recargan cuando el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="d098f-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="d098f-245">Para deshabilitar este comportamiento de recarga en ASP.NET Core 5.0 o versiones posteriores, establezca la clave `hostBuilder:reloadConfigOnChange` en `false`.</span><span class="sxs-lookup"><span data-stu-id="d098f-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="d098f-246">**Clave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d098f-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d098f-247">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-248">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="d098f-248">**Default**: `true`</span></span>  
<span data-ttu-id="d098f-249">**Argumento de la línea de comandos**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d098f-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d098f-250">**Variable de entorno**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d098f-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="d098f-251">El separador de dos puntos (`:`) no funciona con las claves jerárquicas de variables de entorno en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="d098f-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="d098f-252">Para más información, consulte [Variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d098f-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="d098f-253">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="d098f-253">Settings for web apps</span></span>

<span data-ttu-id="d098f-254">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d098f-255">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d098f-256">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="d098f-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d098f-257">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d098f-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d098f-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d098f-258">CaptureStartupErrors</span></span>

<span data-ttu-id="d098f-259">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="d098f-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d098f-260">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="d098f-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d098f-261">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d098f-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d098f-262">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-263">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="d098f-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d098f-264">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d098f-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d098f-265">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="d098f-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d098f-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d098f-266">DetailedErrors</span></span>

<span data-ttu-id="d098f-267">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="d098f-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d098f-268">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d098f-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d098f-269">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-270">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="d098f-270">**Default**: `false`</span></span>  
<span data-ttu-id="d098f-271">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d098f-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d098f-272">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d098f-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d098f-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="d098f-274">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d098f-275">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d098f-276">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d098f-277">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d098f-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d098f-278">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-278">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-279">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="d098f-279">**Default**: Empty string</span></span>  
<span data-ttu-id="d098f-280">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d098f-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d098f-281">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d098f-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d098f-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d098f-283">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d098f-284">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d098f-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d098f-285">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-285">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-286">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="d098f-286">**Default**: Empty string</span></span>  
<span data-ttu-id="d098f-287">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d098f-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d098f-288">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d098f-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d098f-289">HTTPS_Port</span></span>

<span data-ttu-id="d098f-290">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d098f-290">The HTTPS redirect port.</span></span> <span data-ttu-id="d098f-291">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d098f-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d098f-292">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="d098f-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="d098f-293">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-293">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-294">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d098f-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d098f-295">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d098f-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d098f-296">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d098f-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d098f-297">PreferHostingUrls</span></span>

<span data-ttu-id="d098f-298">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="d098f-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d098f-299">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d098f-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d098f-300">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-301">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="d098f-301">**Default**: `true`</span></span>  
<span data-ttu-id="d098f-302">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d098f-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d098f-303">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="d098f-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d098f-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d098f-304">PreventHostingStartup</span></span>

<span data-ttu-id="d098f-305">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d098f-306">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d098f-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d098f-307">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d098f-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d098f-308">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-309">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="d098f-309">**Default**: `false`</span></span>  
<span data-ttu-id="d098f-310">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d098f-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d098f-311">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d098f-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d098f-312">StartupAssembly</span></span>

<span data-ttu-id="d098f-313">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d098f-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d098f-314">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d098f-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d098f-315">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-315">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-316">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="d098f-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d098f-317">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d098f-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d098f-318">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d098f-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d098f-319">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d098f-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d098f-320">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="d098f-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d098f-321">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="d098f-321">URLs</span></span>

<span data-ttu-id="d098f-322">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d098f-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d098f-323">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d098f-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d098f-324">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d098f-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d098f-325">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="d098f-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d098f-326">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="d098f-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="d098f-327">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="d098f-327">**Key**: `urls`</span></span>  
<span data-ttu-id="d098f-328">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-328">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-329">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d098f-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d098f-330">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d098f-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d098f-331">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="d098f-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d098f-332">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="d098f-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d098f-333">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d098f-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d098f-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-334">WebRoot</span></span>

<span data-ttu-id="d098f-335">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d098f-336">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="d098f-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d098f-337">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="d098f-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="d098f-338">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-338">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-339">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d098f-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d098f-340">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d098f-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d098f-341">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d098f-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d098f-342">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d098f-343">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d098f-343">For more information, see:</span></span>

* [<span data-ttu-id="d098f-344">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="d098f-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d098f-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d098f-346">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="d098f-346">Manage the host lifetime</span></span>

<span data-ttu-id="d098f-347">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d098f-348">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d098f-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d098f-349">Run</span><span class="sxs-lookup"><span data-stu-id="d098f-349">Run</span></span>

<span data-ttu-id="d098f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="d098f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d098f-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-351">RunAsync</span></span>

<span data-ttu-id="d098f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="d098f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d098f-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-353">RunConsoleAsync</span></span>

<span data-ttu-id="d098f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d098f-355">Iniciar</span><span class="sxs-lookup"><span data-stu-id="d098f-355">Start</span></span>

<span data-ttu-id="d098f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="d098f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d098f-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-357">StartAsync</span></span>

<span data-ttu-id="d098f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="d098f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d098f-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="d098f-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d098f-360">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="d098f-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d098f-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-361">StopAsync</span></span>

<span data-ttu-id="d098f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="d098f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d098f-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d098f-363">WaitForShutdown</span></span>

<span data-ttu-id="d098f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d098f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d098f-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="d098f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d098f-367">Control externo</span><span class="sxs-lookup"><span data-stu-id="d098f-367">External control</span></span>

<span data-ttu-id="d098f-368">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="d098f-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d098f-369">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="d098f-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d098f-370">En este tema se proporciona información sobre cómo usar el host genérico de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d098f-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d098f-371">Para obtener información sobre cómo usar un host genérico de .NET en aplicaciones de consola, consulte [Host genérico de .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d098f-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d098f-372">Definición de host</span><span class="sxs-lookup"><span data-stu-id="d098f-372">Host definition</span></span>

<span data-ttu-id="d098f-373">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="d098f-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d098f-374">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="d098f-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="d098f-375">Registro</span><span class="sxs-lookup"><span data-stu-id="d098f-375">Logging</span></span>
* <span data-ttu-id="d098f-376">Configuración</span><span class="sxs-lookup"><span data-stu-id="d098f-376">Configuration</span></span>
* <span data-ttu-id="d098f-377">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d098f-377">`IHostedService` implementations</span></span>

<span data-ttu-id="d098f-378">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d098f-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d098f-379">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d098f-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d098f-380">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d098f-381">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="d098f-381">Set up a host</span></span>

<span data-ttu-id="d098f-382">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="d098f-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d098f-383">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-383">The `Main` method:</span></span>

* <span data-ttu-id="d098f-384">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d098f-385">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d098f-386">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host genérico:</span><span class="sxs-lookup"><span data-stu-id="d098f-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d098f-387">En el código siguiente se crea un host genérico mediante una carga de trabajo que no es HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="d098f-388">La implementación de `IHostedService` se agrega al contenedor de DI:</span><span class="sxs-lookup"><span data-stu-id="d098f-388">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d098f-389">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="d098f-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d098f-390">El código anterior se genera con las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d098f-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="d098f-391">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d098f-392">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d098f-393">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d098f-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d098f-394">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="d098f-394">Default builder settings</span></span>

<span data-ttu-id="d098f-395">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="d098f-396">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span><span class="sxs-lookup"><span data-stu-id="d098f-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="d098f-397">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="d098f-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="d098f-398">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d098f-399">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d098f-399">Command-line arguments.</span></span>
* <span data-ttu-id="d098f-400">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="d098f-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="d098f-401">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="d098f-402">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d098f-403">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="d098f-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d098f-404">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-404">Environment variables.</span></span>
  * <span data-ttu-id="d098f-405">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d098f-405">Command-line arguments.</span></span>
* <span data-ttu-id="d098f-406">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="d098f-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d098f-407">Consola</span><span class="sxs-lookup"><span data-stu-id="d098f-407">Console</span></span>
  * <span data-ttu-id="d098f-408">Depuración</span><span class="sxs-lookup"><span data-stu-id="d098f-408">Debug</span></span>
  * <span data-ttu-id="d098f-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="d098f-409">EventSource</span></span>
  * <span data-ttu-id="d098f-410">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="d098f-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d098f-411">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d098f-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d098f-412">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d098f-413">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d098f-414">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d098f-415">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d098f-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d098f-416">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d098f-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d098f-417">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="d098f-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d098f-418">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="d098f-418">Enables IIS integration.</span></span> <span data-ttu-id="d098f-419">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d098f-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d098f-420">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="d098f-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d098f-421">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="d098f-421">Framework-provided services</span></span>

<span data-ttu-id="d098f-422">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="d098f-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d098f-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d098f-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d098f-425">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d098f-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d098f-426">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="d098f-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d098f-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="d098f-428">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d098f-429">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="d098f-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d098f-430">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d098f-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d098f-431">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d098f-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d098f-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-432">IHostLifetime</span></span>

<span data-ttu-id="d098f-433">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="d098f-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d098f-434">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="d098f-434">The last implementation registered is used.</span></span>

<span data-ttu-id="d098f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="d098f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d098f-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d098f-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d098f-437">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="d098f-438">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d098f-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d098f-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d098f-439">IHostEnvironment</span></span>

<span data-ttu-id="d098f-440">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="d098f-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d098f-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d098f-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d098f-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d098f-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d098f-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d098f-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d098f-444">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d098f-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d098f-445">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="d098f-445">Host configuration</span></span>

<span data-ttu-id="d098f-446">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d098f-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d098f-447">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="d098f-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="d098f-448">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d098f-449">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="d098f-450">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-451">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d098f-452">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d098f-453">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d098f-454">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d098f-455">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="d098f-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d098f-456">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="d098f-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d098f-457">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="d098f-457">App configuration</span></span>

<span data-ttu-id="d098f-458">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d098f-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d098f-459">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-460">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d098f-461">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="d098f-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d098f-462">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d098f-463">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d098f-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d098f-464">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="d098f-464">Settings for all app types</span></span>

<span data-ttu-id="d098f-465">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d098f-466">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d098f-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d098f-467">ApplicationName</span></span>

<span data-ttu-id="d098f-468">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="d098f-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d098f-469">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d098f-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d098f-470">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-470">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-471">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d098f-472">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d098f-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d098f-473">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d098f-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-474">ContentRoot</span></span>

<span data-ttu-id="d098f-475">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="d098f-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d098f-476">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="d098f-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d098f-477">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d098f-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d098f-478">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-478">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-479">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d098f-480">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d098f-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d098f-481">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d098f-482">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d098f-482">For more information, see:</span></span>

* [<span data-ttu-id="d098f-483">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="d098f-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d098f-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d098f-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d098f-485">EnvironmentName</span></span>

<span data-ttu-id="d098f-486">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="d098f-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d098f-487">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="d098f-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d098f-488">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d098f-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d098f-489">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d098f-489">**Key**: `environment`</span></span>  
<span data-ttu-id="d098f-490">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-490">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-491">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d098f-491">**Default**: `Production`</span></span>  
<span data-ttu-id="d098f-492">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d098f-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d098f-493">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d098f-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d098f-494">ShutdownTimeout</span></span>

<span data-ttu-id="d098f-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d098f-496">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="d098f-496">The default value is five seconds.</span></span>  <span data-ttu-id="d098f-497">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="d098f-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="d098f-498">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d098f-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d098f-499">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="d098f-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d098f-500">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d098f-501">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="d098f-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d098f-502">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d098f-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d098f-503">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d098f-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d098f-504">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="d098f-504">**Type**: `int`</span></span>  
<span data-ttu-id="d098f-505">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="d098f-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d098f-506">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d098f-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d098f-507">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d098f-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d098f-508">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="d098f-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d098f-509">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="d098f-509">Settings for web apps</span></span>

<span data-ttu-id="d098f-510">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d098f-511">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d098f-512">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="d098f-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d098f-513">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d098f-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d098f-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d098f-514">CaptureStartupErrors</span></span>

<span data-ttu-id="d098f-515">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="d098f-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d098f-516">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="d098f-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d098f-517">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d098f-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d098f-518">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-519">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="d098f-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d098f-520">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d098f-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d098f-521">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="d098f-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d098f-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d098f-522">DetailedErrors</span></span>

<span data-ttu-id="d098f-523">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="d098f-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d098f-524">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d098f-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d098f-525">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-526">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="d098f-526">**Default**: `false`</span></span>  
<span data-ttu-id="d098f-527">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d098f-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d098f-528">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d098f-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d098f-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="d098f-530">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d098f-531">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d098f-532">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d098f-533">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d098f-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d098f-534">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-534">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-535">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="d098f-535">**Default**: Empty string</span></span>  
<span data-ttu-id="d098f-536">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d098f-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d098f-537">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d098f-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d098f-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d098f-539">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="d098f-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d098f-540">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d098f-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d098f-541">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-541">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-542">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="d098f-542">**Default**: Empty string</span></span>  
<span data-ttu-id="d098f-543">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d098f-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d098f-544">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d098f-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d098f-545">HTTPS_Port</span></span>

<span data-ttu-id="d098f-546">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d098f-546">The HTTPS redirect port.</span></span> <span data-ttu-id="d098f-547">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d098f-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d098f-548">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="d098f-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="d098f-549">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-549">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-550">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="d098f-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d098f-551">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d098f-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d098f-552">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d098f-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d098f-553">PreferHostingUrls</span></span>

<span data-ttu-id="d098f-554">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="d098f-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d098f-555">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d098f-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d098f-556">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-557">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="d098f-557">**Default**: `true`</span></span>  
<span data-ttu-id="d098f-558">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d098f-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d098f-559">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="d098f-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d098f-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d098f-560">PreventHostingStartup</span></span>

<span data-ttu-id="d098f-561">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d098f-562">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d098f-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d098f-563">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d098f-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d098f-564">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="d098f-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d098f-565">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="d098f-565">**Default**: `false`</span></span>  
<span data-ttu-id="d098f-566">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d098f-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d098f-567">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d098f-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d098f-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d098f-568">StartupAssembly</span></span>

<span data-ttu-id="d098f-569">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d098f-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d098f-570">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d098f-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d098f-571">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-571">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-572">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="d098f-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d098f-573">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d098f-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d098f-574">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d098f-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d098f-575">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d098f-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d098f-576">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="d098f-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d098f-577">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="d098f-577">URLs</span></span>

<span data-ttu-id="d098f-578">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d098f-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d098f-579">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d098f-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d098f-580">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d098f-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d098f-581">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="d098f-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d098f-582">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="d098f-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="d098f-583">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="d098f-583">**Key**: `urls`</span></span>  
<span data-ttu-id="d098f-584">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-584">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-585">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d098f-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d098f-586">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d098f-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d098f-587">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="d098f-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d098f-588">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="d098f-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d098f-589">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d098f-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d098f-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-590">WebRoot</span></span>

<span data-ttu-id="d098f-591">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d098f-592">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="d098f-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d098f-593">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="d098f-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="d098f-594">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-594">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-595">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d098f-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d098f-596">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d098f-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d098f-597">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d098f-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d098f-598">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d098f-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d098f-599">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d098f-599">For more information, see:</span></span>

* [<span data-ttu-id="d098f-600">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="d098f-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d098f-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d098f-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d098f-602">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="d098f-602">Manage the host lifetime</span></span>

<span data-ttu-id="d098f-603">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d098f-604">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d098f-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d098f-605">Run</span><span class="sxs-lookup"><span data-stu-id="d098f-605">Run</span></span>

<span data-ttu-id="d098f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="d098f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d098f-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-607">RunAsync</span></span>

<span data-ttu-id="d098f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="d098f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d098f-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-609">RunConsoleAsync</span></span>

<span data-ttu-id="d098f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d098f-611">Iniciar</span><span class="sxs-lookup"><span data-stu-id="d098f-611">Start</span></span>

<span data-ttu-id="d098f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="d098f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d098f-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-613">StartAsync</span></span>

<span data-ttu-id="d098f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="d098f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d098f-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="d098f-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d098f-616">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="d098f-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d098f-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-617">StopAsync</span></span>

<span data-ttu-id="d098f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="d098f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d098f-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d098f-619">WaitForShutdown</span></span>

<span data-ttu-id="d098f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d098f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d098f-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="d098f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d098f-623">Control externo</span><span class="sxs-lookup"><span data-stu-id="d098f-623">External control</span></span>

<span data-ttu-id="d098f-624">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="d098f-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d098f-625">Las aplicaciones ASP.NET Core configuran e inician un host.</span><span class="sxs-lookup"><span data-stu-id="d098f-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d098f-626">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d098f-627">En este artículo se trata el host genérico de ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que se usa para hospedar aplicaciones que no procesan solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d098f-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d098f-628">El objetivo del host genérico es desacoplar la canalización HTTP de la API host web para habilitar una mayor variedad de escenarios de host.</span><span class="sxs-lookup"><span data-stu-id="d098f-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d098f-629">La mensajería, las tareas en segundo plano y otras cargas de trabajo que no son de HTTP basadas en la ventaja de host genérico se benefician de funcionalidades transversales, como la configuración, la inserción de dependencias (DI) y el registro.</span><span class="sxs-lookup"><span data-stu-id="d098f-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d098f-630">El host genérico es una novedad de ASP.NET Core 2.1 y no es adecuado para escenarios de hospedaje web.</span><span class="sxs-lookup"><span data-stu-id="d098f-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d098f-631">Para escenarios de hospedaje web, use el [host web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d098f-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d098f-632">El host genérico reemplazará al host web en una próxima versión y actuará como la API de host principal tanto en escenarios de HTTP como los que no lo son.</span><span class="sxs-lookup"><span data-stu-id="d098f-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d098f-633">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d098f-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d098f-634">Al ejecutar la aplicación de ejemplo en [Visual Studio Code](https://code.visualstudio.com/), use un *terminal integrado o externo*.</span><span class="sxs-lookup"><span data-stu-id="d098f-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d098f-635">No ejecute el ejemplo en `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="d098f-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d098f-636">Para establecer la consola en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="d098f-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d098f-637">Abra el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d098f-638">En la configuración de **.NET Core Launch (console)** , busque la entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="d098f-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d098f-639">Establezca el valor en `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="d098f-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d098f-640">Introducción</span><span class="sxs-lookup"><span data-stu-id="d098f-640">Introduction</span></span>

<span data-ttu-id="d098f-641">La biblioteca de host genérico está disponible en el espacio de nombres <xref:Microsoft.Extensions.Hosting> y la proporciona el paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="d098f-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d098f-642">El paquete `Microsoft.Extensions.Hosting` está incluido en el metapaquete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o posterior).</span><span class="sxs-lookup"><span data-stu-id="d098f-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d098f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> es el punto de entrada para la ejecución de código.</span><span class="sxs-lookup"><span data-stu-id="d098f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d098f-644">Cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> se ejecuta en el orden de [registro del servicio en ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="d098f-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d098f-645">Se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> en cada <xref:Microsoft.Extensions.Hosting.IHostedService> cuando se inicia el host, y se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> en orden inverso del registro cuando el host se cierra de forma estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d098f-646">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="d098f-646">Set up a host</span></span>

<span data-ttu-id="d098f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> es el componente principal que usan las bibliotecas y aplicaciones para inicializar, compilar y ejecutar el host:</span><span class="sxs-lookup"><span data-stu-id="d098f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d098f-648">Opciones</span><span class="sxs-lookup"><span data-stu-id="d098f-648">Options</span></span>

<span data-ttu-id="d098f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configura opciones para <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d098f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d098f-650">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="d098f-650">Shutdown timeout</span></span>

<span data-ttu-id="d098f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d098f-652">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="d098f-652">The default value is five seconds.</span></span>

<span data-ttu-id="d098f-653">La siguiente configuración de opción en `Program.Main` aumenta el tiempo de espera de apagado predeterminado de 5 segundos a 20:</span><span class="sxs-lookup"><span data-stu-id="d098f-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="d098f-654">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="d098f-654">Default services</span></span>

<span data-ttu-id="d098f-655">Estos son los servicios que se registran durante la inicialización del host:</span><span class="sxs-lookup"><span data-stu-id="d098f-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d098f-656">[Entorno](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="d098f-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d098f-657">[Configuración](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="d098f-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d098f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d098f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="d098f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d098f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d098f-660">[Opciones](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="d098f-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d098f-661">[Registro](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="d098f-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d098f-662">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="d098f-662">Host configuration</span></span>

<span data-ttu-id="d098f-663">La configuración del host se crea:</span><span class="sxs-lookup"><span data-stu-id="d098f-663">Host configuration is created by:</span></span>

* <span data-ttu-id="d098f-664">Llamando a métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder> para establecer la [raíz del contenido](#content-root) y el [entorno](#environment).</span><span class="sxs-lookup"><span data-stu-id="d098f-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d098f-665">Leyendo la configuración desde los proveedores de configuración de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d098f-666">Métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="d098f-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d098f-667">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="d098f-667">Application key (name)</span></span>

<span data-ttu-id="d098f-668">La propiedad [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) se establece desde la configuración del host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="d098f-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d098f-669">Para establecer el valor explícitamente, use [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="d098f-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d098f-670">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d098f-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d098f-671">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-671">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-672">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d098f-673">**Establecer mediante**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d098f-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d098f-674">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d098f-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d098f-675">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="d098f-675">Content root</span></span>

<span data-ttu-id="d098f-676">Esta configuración determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="d098f-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d098f-677">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d098f-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d098f-678">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-678">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-679">**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d098f-680">**Establecer mediante**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d098f-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d098f-681">**Variable de entorno**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d098f-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d098f-682">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="d098f-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="d098f-683">Para más información, consulte [Aspectos básicos: Raíz del contenido](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="d098f-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="d098f-684">Entorno</span><span class="sxs-lookup"><span data-stu-id="d098f-684">Environment</span></span>

<span data-ttu-id="d098f-685">Establece el [entorno](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d098f-686">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d098f-686">**Key**: `environment`</span></span>  
<span data-ttu-id="d098f-687">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="d098f-687">**Type**: `string`</span></span>  
<span data-ttu-id="d098f-688">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d098f-688">**Default**: `Production`</span></span>  
<span data-ttu-id="d098f-689">**Establecer mediante**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d098f-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d098f-690">**Variable de entorno**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d098f-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d098f-691">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="d098f-691">The environment can be set to any value.</span></span> <span data-ttu-id="d098f-692">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="d098f-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d098f-693">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d098f-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d098f-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d098f-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d098f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para el host.</span><span class="sxs-lookup"><span data-stu-id="d098f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d098f-696">La configuración del host se usa para inicializar el <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para su uso en el proceso de compilación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d098f-697">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-698">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d098f-699">De forma predeterminada no se incluye ningún proveedor.</span><span class="sxs-lookup"><span data-stu-id="d098f-699">No providers are included by default.</span></span> <span data-ttu-id="d098f-700">Debe especificar de forma explícita los proveedores de configuración que requiere la aplicación en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, así como:</span><span class="sxs-lookup"><span data-stu-id="d098f-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d098f-701">La configuración de archivo (por ejemplo, de un archivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="d098f-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d098f-702">La configuración de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-702">Environment variable configuration.</span></span>
* <span data-ttu-id="d098f-703">La configuración del argumento de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="d098f-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="d098f-704">Otros proveedores de configuración necesarios.</span><span class="sxs-lookup"><span data-stu-id="d098f-704">Any other required configuration providers.</span></span>

<span data-ttu-id="d098f-705">La configuración de archivo del host se habilita especificando la ruta base de la aplicación con `SetBasePath` seguido de una llamada a uno de los [proveedores de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d098f-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d098f-706">La aplicación de ejemplo usa un archivo JSON, *hostsettings.json*, y llama a <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir los valores de configuración del host del archivo.</span><span class="sxs-lookup"><span data-stu-id="d098f-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d098f-707">Para agregar una [configuración de la variable de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) del host, llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en el generador del host.</span><span class="sxs-lookup"><span data-stu-id="d098f-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d098f-708">`AddEnvironmentVariables` acepta un prefijo opcional definido por el usuario.</span><span class="sxs-lookup"><span data-stu-id="d098f-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d098f-709">La aplicación de ejemplo usa el prefijo `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d098f-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d098f-710">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="d098f-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d098f-711">Al configurar el host de la aplicación de ejemplo, el valor de la variable de entorno de `PREFIX_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="d098f-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d098f-712">Durante el desarrollo, al usar [Visual Studio](https://visualstudio.microsoft.com) o al ejecutar una aplicación con `dotnet run`, se pueden establecer variables de entorno en el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d098f-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d098f-713">En [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json* durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d098f-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d098f-714">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d098f-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d098f-715">La [configuración de la línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) se agrega llamando a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d098f-716">La configuración de la línea de comandos se agrega en último lugar para permitir que los argumentos de la línea de comandos invaliden la configuración proporcionada por los proveedores de configuración anteriores.</span><span class="sxs-lookup"><span data-stu-id="d098f-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d098f-717">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d098f-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d098f-718">Se puede proporcionar una configuración adicional con las claves [applicationName](#application-key-name) y [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="d098f-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d098f-719">Configuración de `HostBuilder` de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d098f-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d098f-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d098f-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d098f-721">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en la implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d098f-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d098f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d098f-723">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d098f-724">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="d098f-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d098f-725">La configuración creada por <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y en <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d098f-726">La configuración de la aplicación recibe automáticamente la configuración del host proporcionada por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d098f-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d098f-727">Configuración de aplicación de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d098f-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d098f-728">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d098f-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d098f-729">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="d098f-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d098f-730">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="d098f-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d098f-731">Para mover los archivos de configuración al directorio de salida, especifique los archivos de configuración como [elementos de proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="d098f-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d098f-732">La aplicación de ejemplo mueve sus archivos de configuración de aplicación JSON y *hostsettings.json* con el elemento `<Content>` siguiente:</span><span class="sxs-lookup"><span data-stu-id="d098f-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d098f-733">Los métodos de extensión de configuración, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> y <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> requieren paquetes NuGet adicionales, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) y [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="d098f-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d098f-734">A menos que la aplicación use el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), además del paquete principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), se deben agregar estos paquetes.</span><span class="sxs-lookup"><span data-stu-id="d098f-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d098f-735">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d098f-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d098f-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d098f-736">ConfigureServices</span></span>

<span data-ttu-id="d098f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> agrega los servicios al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d098f-738">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d098f-739">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d098f-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d098f-740">Para obtener más información, vea <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d098f-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d098f-741">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa el método de extensión `AddHostedService` para agregar un servicio para eventos de duración, `LifetimeEventsHostedService`, y una tarea en segundo plano programada, `TimedHostedService`, a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d098f-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d098f-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d098f-742">ConfigureLogging</span></span>

<span data-ttu-id="d098f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> agrega un delegado para configurar el <xref:Microsoft.Extensions.Logging.ILoggingBuilder> proporcionado.</span><span class="sxs-lookup"><span data-stu-id="d098f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d098f-744">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d098f-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-745">UseConsoleLifetime</span></span>

<span data-ttu-id="d098f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d098f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d098f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d098f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ya está registrado previamente como la implementación de duración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d098f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d098f-749">Se usa la última duración registrada.</span><span class="sxs-lookup"><span data-stu-id="d098f-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d098f-750">Configuración del contenedor</span><span class="sxs-lookup"><span data-stu-id="d098f-750">Container configuration</span></span>

<span data-ttu-id="d098f-751">Para permitir la conexión a otros contenedores, el host puede aceptar <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="d098f-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d098f-752">Proporcionar un generador no forma parte del registro de contenedor DI, sino que es un host intrínseco utilizado para crear el contenedor DI determinado.</span><span class="sxs-lookup"><span data-stu-id="d098f-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d098f-753">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) invalida el generador predeterminado utilizado para crear el proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d098f-754">La configuración personalizada del contenedor está administrada por el método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d098f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> proporciona una experiencia fuertemente tipada para configurar el contenedor sobre la API de host subyacente.</span><span class="sxs-lookup"><span data-stu-id="d098f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d098f-756">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="d098f-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d098f-757">Crear un contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d098f-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d098f-758">Proporcionar un generador de contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="d098f-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d098f-759">Usar el generador y configurar el contenedor de servicio personalizado de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d098f-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d098f-760">Extensibilidad</span><span class="sxs-lookup"><span data-stu-id="d098f-760">Extensibility</span></span>

<span data-ttu-id="d098f-761">La extensibilidad de host se realiza con métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d098f-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d098f-762">En el ejemplo siguiente se muestra cómo un método de extensión extiende una implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder> con el ejemplo [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) demostrado en <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d098f-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d098f-763">Una aplicación establece el método de extensión `UseHostedService` para registrar el servicio hospedado pasado en `T`:</span><span class="sxs-lookup"><span data-stu-id="d098f-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="d098f-764">Administración del host</span><span class="sxs-lookup"><span data-stu-id="d098f-764">Manage the host</span></span>

<span data-ttu-id="d098f-765">La implementación de <xref:Microsoft.Extensions.Hosting.IHost> es la responsable de iniciar y detener las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> que están registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="d098f-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d098f-766">Run</span><span class="sxs-lookup"><span data-stu-id="d098f-766">Run</span></span>

<span data-ttu-id="d098f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host:</span><span class="sxs-lookup"><span data-stu-id="d098f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="d098f-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-768">RunAsync</span></span>

<span data-ttu-id="d098f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre:</span><span class="sxs-lookup"><span data-stu-id="d098f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="d098f-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-770">RunConsoleAsync</span></span>

<span data-ttu-id="d098f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="d098f-772">Start y StopAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-772">Start and StopAsync</span></span>

<span data-ttu-id="d098f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="d098f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d098f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="d098f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d098f-775">StartAsync y StopAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="d098f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d098f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> detiene la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="d098f-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d098f-778">WaitForShutdown</span></span>

<span data-ttu-id="d098f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se desencadena mediante <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d098f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d098f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="d098f-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d098f-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="d098f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d098f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="d098f-783">Control externo</span><span class="sxs-lookup"><span data-stu-id="d098f-783">External control</span></span>

<span data-ttu-id="d098f-784">El control externo del host se puede lograr mediante métodos a los que se pueda llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="d098f-784">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="d098f-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="d098f-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d098f-786">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="d098f-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d098f-787">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="d098f-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="d098f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> proporciona información sobre el entorno de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d098f-789">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="d098f-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="d098f-790">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d098f-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d098f-791">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d098f-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="d098f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite actividades posteriores al inicio y cierre, incluidas las solicitudes de cierre estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d098f-793">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos <xref:System.Action> que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="d098f-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d098f-794">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="d098f-794">Cancellation Token</span></span> | <span data-ttu-id="d098f-795">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="d098f-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d098f-796">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="d098f-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d098f-797">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d098f-798">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d098f-798">All requests should be processed.</span></span> <span data-ttu-id="d098f-799">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="d098f-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d098f-800">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="d098f-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d098f-801">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d098f-801">Requests may still be processing.</span></span> <span data-ttu-id="d098f-802">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="d098f-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d098f-803">Inserción de constructor del servicio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> en cualquier clase.</span><span class="sxs-lookup"><span data-stu-id="d098f-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d098f-804">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) se usa la inserción de constructor en una clase `LifetimeEventsHostedService` (una implementación de <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar los eventos.</span><span class="sxs-lookup"><span data-stu-id="d098f-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d098f-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d098f-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d098f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d098f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d098f-807">La siguiente clase usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="d098f-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d098f-808">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d098f-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
