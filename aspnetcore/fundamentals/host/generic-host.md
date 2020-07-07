---
title: Host genérico de .NET
author: rick-anderson
description: Obtenga información sobre el host genérico .NET Core, que es responsable de la administración de inicio y duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 5a2d39af6c921323ae9113fd4aca27dcdedd44a5
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793464"
---
# <a name="net-generic-host"></a><span data-ttu-id="60831-103">Host genérico de .NET</span><span class="sxs-lookup"><span data-stu-id="60831-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="60831-104">Las plantillas de ASP.NET Core crean un host genérico de .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="60831-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="60831-105">Definición de host</span><span class="sxs-lookup"><span data-stu-id="60831-105">Host definition</span></span>

<span data-ttu-id="60831-106">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="60831-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="60831-107">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="60831-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="60831-108">Registro</span><span class="sxs-lookup"><span data-stu-id="60831-108">Logging</span></span>
* <span data-ttu-id="60831-109">Configuración</span><span class="sxs-lookup"><span data-stu-id="60831-109">Configuration</span></span>
* <span data-ttu-id="60831-110">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="60831-110">`IHostedService` implementations</span></span>

<span data-ttu-id="60831-111">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="60831-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="60831-112">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="60831-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="60831-113">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="60831-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="60831-114">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="60831-114">Set up a host</span></span>

<span data-ttu-id="60831-115">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="60831-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="60831-116">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-116">The `Main` method:</span></span>

* <span data-ttu-id="60831-117">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="60831-118">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="60831-119">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host genérico:</span><span class="sxs-lookup"><span data-stu-id="60831-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="60831-120">En el código siguiente se crea un host genérico mediante una carga de trabajo que no es HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="60831-121">La implementación de `IHostedService` se agrega al contenedor de DI:</span><span class="sxs-lookup"><span data-stu-id="60831-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="60831-122">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="60831-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="60831-123">El código anterior se genera con las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60831-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="60831-124">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="60831-125">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="60831-126">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="60831-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="60831-127">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="60831-127">Default builder settings</span></span>

<span data-ttu-id="60831-128">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="60831-129">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="60831-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="60831-130">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="60831-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="60831-131">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="60831-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="60831-132">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="60831-132">Command-line arguments.</span></span>
* <span data-ttu-id="60831-133">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="60831-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="60831-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="60831-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="60831-136">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="60831-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="60831-137">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-137">Environment variables.</span></span>
  * <span data-ttu-id="60831-138">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="60831-138">Command-line arguments.</span></span>
* <span data-ttu-id="60831-139">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="60831-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="60831-140">Consola</span><span class="sxs-lookup"><span data-stu-id="60831-140">Console</span></span>
  * <span data-ttu-id="60831-141">Depuración</span><span class="sxs-lookup"><span data-stu-id="60831-141">Debug</span></span>
  * <span data-ttu-id="60831-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="60831-142">EventSource</span></span>
  * <span data-ttu-id="60831-143">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="60831-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="60831-144">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="60831-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="60831-145">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="60831-146">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="60831-147">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="60831-148">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="60831-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="60831-149">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="60831-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="60831-150">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="60831-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="60831-151">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="60831-151">Enables IIS integration.</span></span> <span data-ttu-id="60831-152">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="60831-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="60831-153">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="60831-154">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="60831-154">Framework-provided services</span></span>

<span data-ttu-id="60831-155">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="60831-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="60831-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="60831-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="60831-158">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="60831-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="60831-159">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="60831-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="60831-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="60831-161">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="60831-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="60831-162">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="60831-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="60831-163">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="60831-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="60831-164">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="60831-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="60831-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-165">IHostLifetime</span></span>

<span data-ttu-id="60831-166">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="60831-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="60831-167">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="60831-167">The last implementation registered is used.</span></span>

<span data-ttu-id="60831-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="60831-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="60831-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="60831-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="60831-170">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="60831-171">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="60831-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="60831-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="60831-172">IHostEnvironment</span></span>

<span data-ttu-id="60831-173">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="60831-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="60831-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="60831-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="60831-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="60831-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="60831-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="60831-177">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="60831-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="60831-178">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="60831-178">Host configuration</span></span>

<span data-ttu-id="60831-179">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="60831-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="60831-180">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="60831-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="60831-181">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="60831-182">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="60831-183">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-184">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="60831-185">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="60831-186">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="60831-187">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="60831-188">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="60831-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="60831-189">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="60831-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="60831-190">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="60831-190">App configuration</span></span>

<span data-ttu-id="60831-191">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="60831-192">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-193">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="60831-194">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="60831-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="60831-195">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="60831-196">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="60831-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="60831-197">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="60831-197">Settings for all app types</span></span>

<span data-ttu-id="60831-198">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="60831-199">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="60831-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="60831-200">ApplicationName</span></span>

<span data-ttu-id="60831-201">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="60831-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="60831-202">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="60831-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="60831-203">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-203">**Type**: `string`</span></span>  
<span data-ttu-id="60831-204">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="60831-205">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="60831-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="60831-206">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="60831-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="60831-207">ContentRoot</span></span>

<span data-ttu-id="60831-208">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="60831-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="60831-209">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="60831-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="60831-210">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="60831-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="60831-211">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-211">**Type**: `string`</span></span>  
<span data-ttu-id="60831-212">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="60831-213">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="60831-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="60831-214">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="60831-215">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="60831-215">For more information, see:</span></span>

* [<span data-ttu-id="60831-216">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="60831-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="60831-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="60831-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="60831-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="60831-218">EnvironmentName</span></span>

<span data-ttu-id="60831-219">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="60831-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="60831-220">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="60831-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="60831-221">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="60831-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="60831-222">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="60831-222">**Key**: `environment`</span></span>  
<span data-ttu-id="60831-223">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-223">**Type**: `string`</span></span>  
<span data-ttu-id="60831-224">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="60831-224">**Default**: `Production`</span></span>  
<span data-ttu-id="60831-225">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="60831-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="60831-226">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="60831-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="60831-227">ShutdownTimeout</span></span>

<span data-ttu-id="60831-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="60831-229">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="60831-229">The default value is five seconds.</span></span>  <span data-ttu-id="60831-230">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="60831-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="60831-231">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="60831-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="60831-232">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="60831-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="60831-233">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="60831-234">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="60831-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="60831-235">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="60831-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="60831-236">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="60831-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="60831-237">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="60831-237">**Type**: `int`</span></span>  
<span data-ttu-id="60831-238">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="60831-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="60831-239">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="60831-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="60831-240">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="60831-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="60831-241">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="60831-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="60831-242">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="60831-242">Settings for web apps</span></span>

<span data-ttu-id="60831-243">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="60831-244">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="60831-245">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="60831-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="60831-246">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="60831-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="60831-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="60831-247">CaptureStartupErrors</span></span>

<span data-ttu-id="60831-248">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="60831-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="60831-249">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="60831-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="60831-250">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="60831-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="60831-251">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-252">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="60831-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="60831-253">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="60831-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="60831-254">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="60831-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="60831-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="60831-255">DetailedErrors</span></span>

<span data-ttu-id="60831-256">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="60831-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="60831-257">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="60831-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="60831-258">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-259">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="60831-259">**Default**: `false`</span></span>  
<span data-ttu-id="60831-260">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="60831-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="60831-261">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="60831-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="60831-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="60831-263">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="60831-264">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="60831-265">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="60831-266">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="60831-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="60831-267">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-267">**Type**: `string`</span></span>  
<span data-ttu-id="60831-268">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="60831-268">**Default**: Empty string</span></span>  
<span data-ttu-id="60831-269">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="60831-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="60831-270">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="60831-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="60831-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="60831-272">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="60831-273">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="60831-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="60831-274">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-274">**Type**: `string`</span></span>  
<span data-ttu-id="60831-275">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="60831-275">**Default**: Empty string</span></span>  
<span data-ttu-id="60831-276">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="60831-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="60831-277">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="60831-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="60831-278">HTTPS_Port</span></span>

<span data-ttu-id="60831-279">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="60831-279">The HTTPS redirect port.</span></span> <span data-ttu-id="60831-280">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="60831-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="60831-281">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="60831-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="60831-282">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-282">**Type**: `string`</span></span>  
<span data-ttu-id="60831-283">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="60831-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="60831-284">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="60831-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="60831-285">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="60831-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="60831-286">PreferHostingUrls</span></span>

<span data-ttu-id="60831-287">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="60831-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="60831-288">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="60831-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="60831-289">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-290">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="60831-290">**Default**: `true`</span></span>  
<span data-ttu-id="60831-291">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="60831-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="60831-292">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="60831-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="60831-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="60831-293">PreventHostingStartup</span></span>

<span data-ttu-id="60831-294">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="60831-295">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="60831-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="60831-296">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="60831-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="60831-297">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-298">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="60831-298">**Default**: `false`</span></span>  
<span data-ttu-id="60831-299">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="60831-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="60831-300">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="60831-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="60831-301">StartupAssembly</span></span>

<span data-ttu-id="60831-302">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="60831-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="60831-303">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="60831-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="60831-304">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-304">**Type**: `string`</span></span>  
<span data-ttu-id="60831-305">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="60831-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="60831-306">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="60831-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="60831-307">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="60831-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="60831-308">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="60831-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="60831-309">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="60831-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="60831-310">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="60831-310">URLs</span></span>

<span data-ttu-id="60831-311">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="60831-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="60831-312">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="60831-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="60831-313">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="60831-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="60831-314">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="60831-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="60831-315">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="60831-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="60831-316">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="60831-316">**Key**: `urls`</span></span>  
<span data-ttu-id="60831-317">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-317">**Type**: `string`</span></span>  
<span data-ttu-id="60831-318">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="60831-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="60831-319">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="60831-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="60831-320">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="60831-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="60831-321">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="60831-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="60831-322">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="60831-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="60831-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="60831-323">WebRoot</span></span>

<span data-ttu-id="60831-324">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="60831-325">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="60831-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="60831-326">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="60831-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="60831-327">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-327">**Type**: `string`</span></span>  
<span data-ttu-id="60831-328">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="60831-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="60831-329">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="60831-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="60831-330">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="60831-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="60831-331">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="60831-332">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="60831-332">For more information, see:</span></span>

* [<span data-ttu-id="60831-333">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="60831-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="60831-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="60831-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="60831-335">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="60831-335">Manage the host lifetime</span></span>

<span data-ttu-id="60831-336">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="60831-337">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="60831-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="60831-338">Run</span><span class="sxs-lookup"><span data-stu-id="60831-338">Run</span></span>

<span data-ttu-id="60831-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="60831-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="60831-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="60831-340">RunAsync</span></span>

<span data-ttu-id="60831-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="60831-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="60831-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="60831-342">RunConsoleAsync</span></span>

<span data-ttu-id="60831-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="60831-344">Iniciar</span><span class="sxs-lookup"><span data-stu-id="60831-344">Start</span></span>

<span data-ttu-id="60831-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="60831-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="60831-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="60831-346">StartAsync</span></span>

<span data-ttu-id="60831-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="60831-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="60831-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="60831-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="60831-349">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="60831-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="60831-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="60831-350">StopAsync</span></span>

<span data-ttu-id="60831-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="60831-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="60831-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="60831-352">WaitForShutdown</span></span>

<span data-ttu-id="60831-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="60831-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="60831-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="60831-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="60831-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="60831-356">Control externo</span><span class="sxs-lookup"><span data-stu-id="60831-356">External control</span></span>

<span data-ttu-id="60831-357">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="60831-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="60831-358">Las aplicaciones ASP.NET Core configuran e inician un host.</span><span class="sxs-lookup"><span data-stu-id="60831-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="60831-359">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="60831-360">En este artículo se trata el host genérico de ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que se usa para hospedar aplicaciones que no procesan solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="60831-361">El objetivo del host genérico es desacoplar la canalización HTTP de la API host web para habilitar una mayor variedad de escenarios de host.</span><span class="sxs-lookup"><span data-stu-id="60831-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="60831-362">La mensajería, las tareas en segundo plano y otras cargas de trabajo que no son de HTTP basadas en la ventaja de host genérico se benefician de funcionalidades transversales, como la configuración, la inserción de dependencias (DI) y el registro.</span><span class="sxs-lookup"><span data-stu-id="60831-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="60831-363">El host genérico es una novedad de ASP.NET Core 2.1 y no es adecuado para escenarios de hospedaje web.</span><span class="sxs-lookup"><span data-stu-id="60831-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="60831-364">Para escenarios de hospedaje web, use el [host web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="60831-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="60831-365">El host genérico reemplazará al host web en una próxima versión y actuará como la API de host principal tanto en escenarios de HTTP como los que no lo son.</span><span class="sxs-lookup"><span data-stu-id="60831-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="60831-366">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60831-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60831-367">Al ejecutar la aplicación de ejemplo en [Visual Studio Code](https://code.visualstudio.com/), use un *terminal integrado o externo*.</span><span class="sxs-lookup"><span data-stu-id="60831-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="60831-368">No ejecute el ejemplo en `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="60831-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="60831-369">Para establecer la consola en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="60831-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="60831-370">Abra el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="60831-371">En la configuración de **.NET Core Launch (console)** , busque la entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="60831-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="60831-372">Establezca el valor en `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="60831-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="60831-373">Introducción</span><span class="sxs-lookup"><span data-stu-id="60831-373">Introduction</span></span>

<span data-ttu-id="60831-374">La biblioteca de host genérico está disponible en el espacio de nombres <xref:Microsoft.Extensions.Hosting> y la proporciona el paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="60831-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="60831-375">El paquete `Microsoft.Extensions.Hosting` está incluido en el metapaquete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o posterior).</span><span class="sxs-lookup"><span data-stu-id="60831-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="60831-376"><xref:Microsoft.Extensions.Hosting.IHostedService> es el punto de entrada para la ejecución de código.</span><span class="sxs-lookup"><span data-stu-id="60831-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="60831-377">Cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> se ejecuta en el orden de [registro del servicio en ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="60831-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="60831-378">Se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> en cada <xref:Microsoft.Extensions.Hosting.IHostedService> cuando se inicia el host, y se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> en orden inverso del registro cuando el host se cierra de forma estable.</span><span class="sxs-lookup"><span data-stu-id="60831-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="60831-379">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="60831-379">Set up a host</span></span>

<span data-ttu-id="60831-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> es el componente principal que usan las bibliotecas y aplicaciones para inicializar, compilar y ejecutar el host:</span><span class="sxs-lookup"><span data-stu-id="60831-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="60831-381">Opciones</span><span class="sxs-lookup"><span data-stu-id="60831-381">Options</span></span>

<span data-ttu-id="60831-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configura opciones para <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="60831-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="60831-383">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="60831-383">Shutdown timeout</span></span>

<span data-ttu-id="60831-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="60831-385">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="60831-385">The default value is five seconds.</span></span>

<span data-ttu-id="60831-386">La siguiente configuración de opción en `Program.Main` aumenta el tiempo de espera de apagado predeterminado de 5 segundos a 20:</span><span class="sxs-lookup"><span data-stu-id="60831-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="60831-387">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="60831-387">Default services</span></span>

<span data-ttu-id="60831-388">Estos son los servicios que se registran durante la inicialización del host:</span><span class="sxs-lookup"><span data-stu-id="60831-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="60831-389">[Entorno](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="60831-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="60831-390">[Configuración](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="60831-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="60831-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="60831-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="60831-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="60831-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="60831-393">[Opciones](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="60831-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="60831-394">[Registro](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="60831-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="60831-395">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="60831-395">Host configuration</span></span>

<span data-ttu-id="60831-396">La configuración del host se crea:</span><span class="sxs-lookup"><span data-stu-id="60831-396">Host configuration is created by:</span></span>

* <span data-ttu-id="60831-397">Llamando a métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder> para establecer la [raíz del contenido](#content-root) y el [entorno](#environment).</span><span class="sxs-lookup"><span data-stu-id="60831-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="60831-398">Leyendo la configuración desde los proveedores de configuración de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="60831-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="60831-399">Métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="60831-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="60831-400">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="60831-400">Application key (name)</span></span>

<span data-ttu-id="60831-401">La propiedad [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) se establece desde la configuración del host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="60831-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="60831-402">Para establecer el valor explícitamente, use [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="60831-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="60831-403">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="60831-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="60831-404">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-404">**Type**: `string`</span></span>  
<span data-ttu-id="60831-405">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="60831-406">**Establecer mediante**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="60831-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="60831-407">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="60831-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="60831-408">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="60831-408">Content root</span></span>

<span data-ttu-id="60831-409">Esta configuración determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="60831-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="60831-410">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="60831-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="60831-411">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-411">**Type**: `string`</span></span>  
<span data-ttu-id="60831-412">**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="60831-413">**Establecer mediante**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="60831-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="60831-414">**Variable de entorno**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="60831-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="60831-415">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="60831-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="60831-416">Para más información, consulte [Aspectos básicos: Raíz del contenido](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="60831-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="60831-417">Entorno</span><span class="sxs-lookup"><span data-stu-id="60831-417">Environment</span></span>

<span data-ttu-id="60831-418">Establece el [entorno](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="60831-419">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="60831-419">**Key**: `environment`</span></span>  
<span data-ttu-id="60831-420">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-420">**Type**: `string`</span></span>  
<span data-ttu-id="60831-421">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="60831-421">**Default**: `Production`</span></span>  
<span data-ttu-id="60831-422">**Establecer mediante**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="60831-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="60831-423">**Variable de entorno**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="60831-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="60831-424">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="60831-424">The environment can be set to any value.</span></span> <span data-ttu-id="60831-425">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="60831-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="60831-426">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="60831-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="60831-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="60831-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="60831-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para el host.</span><span class="sxs-lookup"><span data-stu-id="60831-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="60831-429">La configuración del host se usa para inicializar el <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para su uso en el proceso de compilación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="60831-430">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-431">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="60831-432">De forma predeterminada no se incluye ningún proveedor.</span><span class="sxs-lookup"><span data-stu-id="60831-432">No providers are included by default.</span></span> <span data-ttu-id="60831-433">Debe especificar de forma explícita los proveedores de configuración que requiere la aplicación en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, así como:</span><span class="sxs-lookup"><span data-stu-id="60831-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="60831-434">La configuración de archivo (por ejemplo, de un archivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="60831-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="60831-435">La configuración de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-435">Environment variable configuration.</span></span>
* <span data-ttu-id="60831-436">La configuración del argumento de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="60831-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="60831-437">Otros proveedores de configuración necesarios.</span><span class="sxs-lookup"><span data-stu-id="60831-437">Any other required configuration providers.</span></span>

<span data-ttu-id="60831-438">La configuración de archivo del host se habilita especificando la ruta base de la aplicación con `SetBasePath` seguido de una llamada a uno de los [proveedores de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="60831-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="60831-439">La aplicación de ejemplo usa un archivo JSON, *hostsettings.json*, y llama a <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir los valores de configuración del host del archivo.</span><span class="sxs-lookup"><span data-stu-id="60831-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="60831-440">Para agregar una [configuración de la variable de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) del host, llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en el generador del host.</span><span class="sxs-lookup"><span data-stu-id="60831-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="60831-441">`AddEnvironmentVariables` acepta un prefijo opcional definido por el usuario.</span><span class="sxs-lookup"><span data-stu-id="60831-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="60831-442">La aplicación de ejemplo usa el prefijo `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="60831-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="60831-443">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="60831-444">Al configurar el host de la aplicación de ejemplo, el valor de la variable de entorno de `PREFIX_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="60831-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="60831-445">Durante el desarrollo, al usar [Visual Studio](https://visualstudio.microsoft.com) o al ejecutar una aplicación con `dotnet run`, se pueden establecer variables de entorno en el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="60831-446">En [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json* durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="60831-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="60831-447">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="60831-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="60831-448">La [configuración de la línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) se agrega llamando a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="60831-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="60831-449">La configuración de la línea de comandos se agrega en último lugar para permitir que los argumentos de la línea de comandos invaliden la configuración proporcionada por los proveedores de configuración anteriores.</span><span class="sxs-lookup"><span data-stu-id="60831-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="60831-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="60831-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="60831-451">Se puede proporcionar una configuración adicional con las claves [applicationName](#application-key-name) y [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="60831-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="60831-452">Configuración de `HostBuilder` de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="60831-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="60831-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="60831-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="60831-454">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en la implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="60831-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="60831-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="60831-456">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-457">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="60831-458">La configuración creada por <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y en <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="60831-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="60831-459">La configuración de la aplicación recibe automáticamente la configuración del host proporcionada por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="60831-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="60831-460">Configuración de aplicación de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="60831-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="60831-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="60831-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="60831-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="60831-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="60831-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="60831-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="60831-464">Para mover los archivos de configuración al directorio de salida, especifique los archivos de configuración como [elementos de proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="60831-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="60831-465">La aplicación de ejemplo mueve sus archivos de configuración de aplicación JSON y *hostsettings.json* con el elemento `<Content>` siguiente:</span><span class="sxs-lookup"><span data-stu-id="60831-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="60831-466">Los métodos de extensión de configuración, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> y <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> requieren paquetes NuGet adicionales, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) y [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="60831-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="60831-467">A menos que la aplicación use el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), además del paquete principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), se deben agregar estos paquetes.</span><span class="sxs-lookup"><span data-stu-id="60831-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="60831-468">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="60831-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="60831-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="60831-469">ConfigureServices</span></span>

<span data-ttu-id="60831-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> agrega los servicios al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="60831-471">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="60831-472">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="60831-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="60831-473">Para obtener más información, vea <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="60831-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="60831-474">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa el método de extensión `AddHostedService` para agregar un servicio para eventos de duración, `LifetimeEventsHostedService`, y una tarea en segundo plano programada, `TimedHostedService`, a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="60831-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="60831-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="60831-475">ConfigureLogging</span></span>

<span data-ttu-id="60831-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> agrega un delegado para configurar el <xref:Microsoft.Extensions.Logging.ILoggingBuilder> proporcionado.</span><span class="sxs-lookup"><span data-stu-id="60831-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="60831-477">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="60831-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-478">UseConsoleLifetime</span></span>

<span data-ttu-id="60831-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="60831-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="60831-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="60831-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ya está registrado previamente como la implementación de duración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="60831-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="60831-482">Se usa la última duración registrada.</span><span class="sxs-lookup"><span data-stu-id="60831-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="60831-483">Configuración del contenedor</span><span class="sxs-lookup"><span data-stu-id="60831-483">Container configuration</span></span>

<span data-ttu-id="60831-484">Para permitir la conexión a otros contenedores, el host puede aceptar <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="60831-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="60831-485">Proporcionar un generador no forma parte del registro de contenedor DI, sino que es un host intrínseco utilizado para crear el contenedor DI determinado.</span><span class="sxs-lookup"><span data-stu-id="60831-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="60831-486">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) invalida el generador predeterminado utilizado para crear el proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="60831-487">La configuración personalizada del contenedor está administrada por el método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="60831-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="60831-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> proporciona una experiencia fuertemente tipada para configurar el contenedor sobre la API de host subyacente.</span><span class="sxs-lookup"><span data-stu-id="60831-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="60831-489">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="60831-490">Crear un contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="60831-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="60831-491">Proporcionar un generador de contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="60831-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="60831-492">Usar el generador y configurar el contenedor de servicio personalizado de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="60831-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="60831-493">Extensibilidad</span><span class="sxs-lookup"><span data-stu-id="60831-493">Extensibility</span></span>

<span data-ttu-id="60831-494">La extensibilidad de host se realiza con métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="60831-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="60831-495">En el ejemplo siguiente se muestra cómo un método de extensión extiende una implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder> con el ejemplo [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) demostrado en <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="60831-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="60831-496">Una aplicación establece el método de extensión `UseHostedService` para registrar el servicio hospedado pasado en `T`:</span><span class="sxs-lookup"><span data-stu-id="60831-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="60831-497">Administración del host</span><span class="sxs-lookup"><span data-stu-id="60831-497">Manage the host</span></span>

<span data-ttu-id="60831-498">La implementación de <xref:Microsoft.Extensions.Hosting.IHost> es la responsable de iniciar y detener las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> que están registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="60831-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="60831-499">Run</span><span class="sxs-lookup"><span data-stu-id="60831-499">Run</span></span>

<span data-ttu-id="60831-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host:</span><span class="sxs-lookup"><span data-stu-id="60831-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="60831-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="60831-501">RunAsync</span></span>

<span data-ttu-id="60831-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre:</span><span class="sxs-lookup"><span data-stu-id="60831-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="60831-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="60831-503">RunConsoleAsync</span></span>

<span data-ttu-id="60831-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="60831-505">Start y StopAsync</span><span class="sxs-lookup"><span data-stu-id="60831-505">Start and StopAsync</span></span>

<span data-ttu-id="60831-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="60831-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="60831-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="60831-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="60831-508">StartAsync y StopAsync</span><span class="sxs-lookup"><span data-stu-id="60831-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="60831-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="60831-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> detiene la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="60831-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="60831-511">WaitForShutdown</span></span>

<span data-ttu-id="60831-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se desencadena mediante <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="60831-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="60831-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="60831-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="60831-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="60831-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="60831-516">Control externo</span><span class="sxs-lookup"><span data-stu-id="60831-516">External control</span></span>

<span data-ttu-id="60831-517">El control externo del host se puede lograr mediante métodos a los que se pueda llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="60831-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="60831-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="60831-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="60831-519">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="60831-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="60831-520">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="60831-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="60831-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> proporciona información sobre el entorno de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="60831-522">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="60831-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="60831-523">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="60831-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="60831-524">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="60831-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite actividades posteriores al inicio y cierre, incluidas las solicitudes de cierre estable.</span><span class="sxs-lookup"><span data-stu-id="60831-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="60831-526">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos <xref:System.Action> que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="60831-527">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="60831-527">Cancellation Token</span></span> | <span data-ttu-id="60831-528">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="60831-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="60831-529">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="60831-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="60831-530">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="60831-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="60831-531">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="60831-531">All requests should be processed.</span></span> <span data-ttu-id="60831-532">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="60831-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="60831-533">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="60831-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="60831-534">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="60831-534">Requests may still be processing.</span></span> <span data-ttu-id="60831-535">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="60831-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="60831-536">Inserción de constructor del servicio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> en cualquier clase.</span><span class="sxs-lookup"><span data-stu-id="60831-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="60831-537">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) se usa la inserción de constructor en una clase `LifetimeEventsHostedService` (una implementación de <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar los eventos.</span><span class="sxs-lookup"><span data-stu-id="60831-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="60831-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="60831-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="60831-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="60831-540">La siguiente clase usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="60831-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="60831-541">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="60831-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="60831-542">Definición de host</span><span class="sxs-lookup"><span data-stu-id="60831-542">Host definition</span></span>

<span data-ttu-id="60831-543">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="60831-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="60831-544">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="60831-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="60831-545">Registro</span><span class="sxs-lookup"><span data-stu-id="60831-545">Logging</span></span>
* <span data-ttu-id="60831-546">Configuración</span><span class="sxs-lookup"><span data-stu-id="60831-546">Configuration</span></span>
* <span data-ttu-id="60831-547">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="60831-547">`IHostedService` implementations</span></span>

<span data-ttu-id="60831-548">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="60831-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="60831-549">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="60831-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="60831-550">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="60831-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="60831-551">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="60831-551">Set up a host</span></span>

<span data-ttu-id="60831-552">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="60831-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="60831-553">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-553">The `Main` method:</span></span>

* <span data-ttu-id="60831-554">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="60831-555">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="60831-556">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host:</span><span class="sxs-lookup"><span data-stu-id="60831-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="60831-557">En el código siguiente se crea una carga de trabajo que no es HTTP con una implementación de `IHostedService` agregada al contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="60831-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="60831-558">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="60831-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="60831-559">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="60831-560">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="60831-561">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="60831-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="60831-562">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="60831-562">Default builder settings</span></span>

<span data-ttu-id="60831-563">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="60831-564">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="60831-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="60831-565">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="60831-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="60831-566">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="60831-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="60831-567">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="60831-567">Command-line arguments.</span></span>
* <span data-ttu-id="60831-568">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="60831-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="60831-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="60831-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="60831-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="60831-571">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="60831-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="60831-572">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-572">Environment variables.</span></span>
  * <span data-ttu-id="60831-573">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="60831-573">Command-line arguments.</span></span>
* <span data-ttu-id="60831-574">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="60831-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="60831-575">Consola</span><span class="sxs-lookup"><span data-stu-id="60831-575">Console</span></span>
  * <span data-ttu-id="60831-576">Depuración</span><span class="sxs-lookup"><span data-stu-id="60831-576">Debug</span></span>
  * <span data-ttu-id="60831-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="60831-577">EventSource</span></span>
  * <span data-ttu-id="60831-578">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="60831-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="60831-579">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="60831-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="60831-580">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="60831-581">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="60831-582">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="60831-583">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="60831-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="60831-584">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="60831-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="60831-585">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="60831-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="60831-586">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="60831-586">Enables IIS integration.</span></span> <span data-ttu-id="60831-587">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="60831-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="60831-588">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="60831-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="60831-589">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="60831-589">Framework-provided services</span></span>

<span data-ttu-id="60831-590">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="60831-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="60831-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="60831-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="60831-593">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="60831-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="60831-594">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="60831-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="60831-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="60831-596">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="60831-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="60831-597">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="60831-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="60831-598">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="60831-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="60831-599">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="60831-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="60831-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="60831-600">IHostLifetime</span></span>

<span data-ttu-id="60831-601">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="60831-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="60831-602">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="60831-602">The last implementation registered is used.</span></span>

<span data-ttu-id="60831-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="60831-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="60831-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="60831-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="60831-605">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="60831-606">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="60831-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="60831-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="60831-607">IHostEnvironment</span></span>

<span data-ttu-id="60831-608">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="60831-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="60831-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="60831-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="60831-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="60831-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="60831-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="60831-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="60831-612">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="60831-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="60831-613">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="60831-613">Host configuration</span></span>

<span data-ttu-id="60831-614">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="60831-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="60831-615">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="60831-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="60831-616">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="60831-617">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="60831-618">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-619">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="60831-620">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="60831-621">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="60831-622">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="60831-623">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="60831-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="60831-624">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="60831-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="60831-625">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="60831-625">App configuration</span></span>

<span data-ttu-id="60831-626">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="60831-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="60831-627">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="60831-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="60831-628">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="60831-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="60831-629">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="60831-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="60831-630">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="60831-631">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="60831-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="60831-632">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="60831-632">Settings for all app types</span></span>

<span data-ttu-id="60831-633">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="60831-634">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="60831-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="60831-635">ApplicationName</span></span>

<span data-ttu-id="60831-636">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="60831-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="60831-637">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="60831-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="60831-638">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-638">**Type**: `string`</span></span>  
<span data-ttu-id="60831-639">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="60831-640">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="60831-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="60831-641">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="60831-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="60831-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="60831-642">ContentRoot</span></span>

<span data-ttu-id="60831-643">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="60831-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="60831-644">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="60831-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="60831-645">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="60831-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="60831-646">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-646">**Type**: `string`</span></span>  
<span data-ttu-id="60831-647">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="60831-648">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="60831-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="60831-649">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="60831-650">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="60831-650">For more information, see:</span></span>

* [<span data-ttu-id="60831-651">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="60831-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="60831-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="60831-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="60831-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="60831-653">EnvironmentName</span></span>

<span data-ttu-id="60831-654">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="60831-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="60831-655">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="60831-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="60831-656">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="60831-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="60831-657">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="60831-657">**Key**: `environment`</span></span>  
<span data-ttu-id="60831-658">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-658">**Type**: `string`</span></span>  
<span data-ttu-id="60831-659">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="60831-659">**Default**: `Production`</span></span>  
<span data-ttu-id="60831-660">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="60831-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="60831-661">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="60831-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="60831-662">ShutdownTimeout</span></span>

<span data-ttu-id="60831-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="60831-664">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="60831-664">The default value is five seconds.</span></span>  <span data-ttu-id="60831-665">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="60831-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="60831-666">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="60831-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="60831-667">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="60831-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="60831-668">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="60831-669">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="60831-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="60831-670">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="60831-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="60831-671">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="60831-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="60831-672">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="60831-672">**Type**: `int`</span></span>  
<span data-ttu-id="60831-673">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="60831-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="60831-674">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="60831-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="60831-675">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="60831-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="60831-676">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="60831-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="60831-677">Deshabilitación de la recarga de configuración de aplicaciones al realizar un cambio</span><span class="sxs-lookup"><span data-stu-id="60831-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="60831-678">[De forma predeterminada](xref:fundamentals/configuration/index#default), *appsettings.json* y *appsettings.{Entorno}.json* se recargan cuando el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="60831-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="60831-679">Para deshabilitar este comportamiento de recarga en ASP.NET Core 5.0 Preview 3 o versiones posteriores, establezca la clave `hostBuilder:reloadConfigOnChange` en `false`.</span><span class="sxs-lookup"><span data-stu-id="60831-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="60831-680">**Clave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="60831-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="60831-681">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-682">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="60831-682">**Default**: `true`</span></span>  
<span data-ttu-id="60831-683">**Argumento de la línea de comandos**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="60831-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="60831-684">**Variable de entorno**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="60831-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="60831-685">El separador de dos puntos (`:`) no funciona con las claves jerárquicas de variables de entorno en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="60831-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="60831-686">Para más información, consulte [Variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="60831-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="60831-687">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="60831-687">Settings for web apps</span></span>

<span data-ttu-id="60831-688">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="60831-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="60831-689">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="60831-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="60831-690">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="60831-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="60831-691">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="60831-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="60831-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="60831-692">CaptureStartupErrors</span></span>

<span data-ttu-id="60831-693">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="60831-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="60831-694">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="60831-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="60831-695">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="60831-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="60831-696">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-697">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="60831-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="60831-698">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="60831-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="60831-699">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="60831-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="60831-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="60831-700">DetailedErrors</span></span>

<span data-ttu-id="60831-701">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="60831-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="60831-702">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="60831-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="60831-703">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-704">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="60831-704">**Default**: `false`</span></span>  
<span data-ttu-id="60831-705">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="60831-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="60831-706">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="60831-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="60831-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="60831-708">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="60831-709">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="60831-710">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="60831-711">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="60831-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="60831-712">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-712">**Type**: `string`</span></span>  
<span data-ttu-id="60831-713">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="60831-713">**Default**: Empty string</span></span>  
<span data-ttu-id="60831-714">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="60831-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="60831-715">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="60831-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="60831-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="60831-717">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="60831-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="60831-718">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="60831-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="60831-719">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-719">**Type**: `string`</span></span>  
<span data-ttu-id="60831-720">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="60831-720">**Default**: Empty string</span></span>  
<span data-ttu-id="60831-721">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="60831-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="60831-722">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="60831-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="60831-723">HTTPS_Port</span></span>

<span data-ttu-id="60831-724">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="60831-724">The HTTPS redirect port.</span></span> <span data-ttu-id="60831-725">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="60831-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="60831-726">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="60831-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="60831-727">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-727">**Type**: `string`</span></span>  
<span data-ttu-id="60831-728">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="60831-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="60831-729">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="60831-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="60831-730">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="60831-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="60831-731">PreferHostingUrls</span></span>

<span data-ttu-id="60831-732">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="60831-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="60831-733">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="60831-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="60831-734">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-735">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="60831-735">**Default**: `true`</span></span>  
<span data-ttu-id="60831-736">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="60831-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="60831-737">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="60831-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="60831-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="60831-738">PreventHostingStartup</span></span>

<span data-ttu-id="60831-739">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="60831-740">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="60831-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="60831-741">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="60831-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="60831-742">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="60831-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="60831-743">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="60831-743">**Default**: `false`</span></span>  
<span data-ttu-id="60831-744">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="60831-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="60831-745">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="60831-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="60831-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="60831-746">StartupAssembly</span></span>

<span data-ttu-id="60831-747">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="60831-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="60831-748">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="60831-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="60831-749">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-749">**Type**: `string`</span></span>  
<span data-ttu-id="60831-750">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="60831-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="60831-751">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="60831-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="60831-752">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="60831-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="60831-753">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="60831-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="60831-754">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="60831-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="60831-755">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="60831-755">URLs</span></span>

<span data-ttu-id="60831-756">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="60831-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="60831-757">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="60831-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="60831-758">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="60831-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="60831-759">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="60831-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="60831-760">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="60831-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="60831-761">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="60831-761">**Key**: `urls`</span></span>  
<span data-ttu-id="60831-762">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-762">**Type**: `string`</span></span>  
<span data-ttu-id="60831-763">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="60831-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="60831-764">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="60831-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="60831-765">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="60831-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="60831-766">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="60831-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="60831-767">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="60831-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="60831-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="60831-768">WebRoot</span></span>

<span data-ttu-id="60831-769">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="60831-770">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="60831-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="60831-771">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="60831-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="60831-772">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="60831-772">**Type**: `string`</span></span>  
<span data-ttu-id="60831-773">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="60831-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="60831-774">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="60831-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="60831-775">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="60831-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="60831-776">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="60831-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="60831-777">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="60831-777">For more information, see:</span></span>

* [<span data-ttu-id="60831-778">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="60831-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="60831-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="60831-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="60831-780">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="60831-780">Manage the host lifetime</span></span>

<span data-ttu-id="60831-781">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="60831-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="60831-782">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="60831-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="60831-783">Run</span><span class="sxs-lookup"><span data-stu-id="60831-783">Run</span></span>

<span data-ttu-id="60831-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="60831-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="60831-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="60831-785">RunAsync</span></span>

<span data-ttu-id="60831-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="60831-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="60831-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="60831-787">RunConsoleAsync</span></span>

<span data-ttu-id="60831-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="60831-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="60831-789">Iniciar</span><span class="sxs-lookup"><span data-stu-id="60831-789">Start</span></span>

<span data-ttu-id="60831-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="60831-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="60831-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="60831-791">StartAsync</span></span>

<span data-ttu-id="60831-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="60831-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="60831-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="60831-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="60831-794">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="60831-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="60831-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="60831-795">StopAsync</span></span>

<span data-ttu-id="60831-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="60831-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="60831-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="60831-797">WaitForShutdown</span></span>

<span data-ttu-id="60831-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="60831-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="60831-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="60831-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="60831-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="60831-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="60831-801">Control externo</span><span class="sxs-lookup"><span data-stu-id="60831-801">External control</span></span>

<span data-ttu-id="60831-802">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="60831-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="60831-803">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="60831-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
