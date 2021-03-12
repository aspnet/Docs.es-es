---
title: Host genérico de .NET en ASP.NET Core
author: rick-anderson
description: Use el host genérico de .NET Core en las aplicaciones de ASP.NET Core.  El host genérico es responsable de la administración del inicio y la duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: ceb766cae1f8a1735f5b578a44f0c481927e47a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586753"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="b7389-104">Host genérico de .NET en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7389-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7389-105">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="b7389-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="b7389-106">En este tema se proporciona información sobre cómo usar el host genérico de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7389-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="b7389-107">Para obtener información sobre cómo usar un host genérico de .NET en aplicaciones de consola, consulte [Host genérico de .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b7389-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="b7389-108">Definición de host</span><span class="sxs-lookup"><span data-stu-id="b7389-108">Host definition</span></span>

<span data-ttu-id="b7389-109">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="b7389-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="b7389-110">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="b7389-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="b7389-111">Registro</span><span class="sxs-lookup"><span data-stu-id="b7389-111">Logging</span></span>
* <span data-ttu-id="b7389-112">Configuración</span><span class="sxs-lookup"><span data-stu-id="b7389-112">Configuration</span></span>
* <span data-ttu-id="b7389-113">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="b7389-113">`IHostedService` implementations</span></span>

<span data-ttu-id="b7389-114">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7389-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="b7389-115">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="b7389-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="b7389-116">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b7389-117">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="b7389-117">Set up a host</span></span>

<span data-ttu-id="b7389-118">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="b7389-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="b7389-119">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-119">The `Main` method:</span></span>

* <span data-ttu-id="b7389-120">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="b7389-121">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="b7389-122">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host:</span><span class="sxs-lookup"><span data-stu-id="b7389-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="b7389-123">En el código siguiente se crea una carga de trabajo que no es HTTP con una implementación de `IHostedService` agregada al contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="b7389-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="b7389-124">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="b7389-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b7389-125">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="b7389-126">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="b7389-127">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="b7389-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="b7389-128">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="b7389-128">Default builder settings</span></span>

<span data-ttu-id="b7389-129">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="b7389-130">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="b7389-131">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="b7389-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="b7389-132">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="b7389-133">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="b7389-133">Command-line arguments.</span></span>
* <span data-ttu-id="b7389-134">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="b7389-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="b7389-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="b7389-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="b7389-137">[Secretos del usuario](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="b7389-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="b7389-138">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-138">Environment variables.</span></span>
  * <span data-ttu-id="b7389-139">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="b7389-139">Command-line arguments.</span></span>
* <span data-ttu-id="b7389-140">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="b7389-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="b7389-141">Consola</span><span class="sxs-lookup"><span data-stu-id="b7389-141">Console</span></span>
  * <span data-ttu-id="b7389-142">Depuración</span><span class="sxs-lookup"><span data-stu-id="b7389-142">Debug</span></span>
  * <span data-ttu-id="b7389-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="b7389-143">EventSource</span></span>
  * <span data-ttu-id="b7389-144">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="b7389-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="b7389-145">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b7389-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="b7389-146">El método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="b7389-147">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="b7389-148">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="b7389-149">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="b7389-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
* <span data-ttu-id="b7389-150">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7389-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering).</span></span>
* <span data-ttu-id="b7389-151">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="b7389-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="b7389-152">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="b7389-152">Enables IIS integration.</span></span> <span data-ttu-id="b7389-153">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="b7389-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="b7389-154">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b7389-155">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="b7389-155">Framework-provided services</span></span>

<span data-ttu-id="b7389-156">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="b7389-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="b7389-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="b7389-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="b7389-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b7389-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="b7389-160">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="b7389-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="b7389-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="b7389-162">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="b7389-163">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="b7389-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="b7389-164">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="b7389-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="b7389-165">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b7389-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="b7389-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-166">IHostLifetime</span></span>

<span data-ttu-id="b7389-167">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="b7389-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="b7389-168">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="b7389-168">The last implementation registered is used.</span></span>

<span data-ttu-id="b7389-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="b7389-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="b7389-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b7389-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="b7389-171">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="b7389-172">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b7389-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="b7389-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b7389-173">IHostEnvironment</span></span>

<span data-ttu-id="b7389-174">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="b7389-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b7389-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="b7389-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b7389-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="b7389-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="b7389-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="b7389-178">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="b7389-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b7389-179">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="b7389-179">Host configuration</span></span>

<span data-ttu-id="b7389-180">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="b7389-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="b7389-181">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="b7389-182">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="b7389-183">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b7389-184">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-185">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b7389-186">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b7389-187">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="b7389-188">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b7389-189">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="b7389-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b7389-190">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="b7389-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="b7389-191">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="b7389-191">App configuration</span></span>

<span data-ttu-id="b7389-192">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b7389-193">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-194">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="b7389-195">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="b7389-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="b7389-196">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="b7389-197">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b7389-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="b7389-198">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="b7389-198">Settings for all app types</span></span>

<span data-ttu-id="b7389-199">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="b7389-200">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="b7389-201">Para más información, consulte [Configuración predeterminada del generador](#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="b7389-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="b7389-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b7389-202">ApplicationName</span></span>

<span data-ttu-id="b7389-203">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="b7389-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="b7389-204">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="b7389-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b7389-205">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-205">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-206">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="b7389-207">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="b7389-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="b7389-208">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="b7389-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-209">ContentRoot</span></span>

<span data-ttu-id="b7389-210">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="b7389-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="b7389-211">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="b7389-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="b7389-212">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b7389-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b7389-213">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-213">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-214">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="b7389-215">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="b7389-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="b7389-216">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="b7389-217">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b7389-217">For more information, see:</span></span>

* [<span data-ttu-id="b7389-218">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="b7389-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="b7389-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="b7389-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b7389-220">EnvironmentName</span></span>

<span data-ttu-id="b7389-221">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="b7389-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="b7389-222">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="b7389-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b7389-223">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b7389-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="b7389-224">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="b7389-224">**Key**: `environment`</span></span>  
<span data-ttu-id="b7389-225">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-225">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-226">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="b7389-226">**Default**: `Production`</span></span>  
<span data-ttu-id="b7389-227">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="b7389-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="b7389-228">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="b7389-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="b7389-229">ShutdownTimeout</span></span>

<span data-ttu-id="b7389-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b7389-231">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="b7389-231">The default value is five seconds.</span></span>  <span data-ttu-id="b7389-232">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="b7389-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="b7389-233">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="b7389-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="b7389-234">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="b7389-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="b7389-235">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="b7389-236">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="b7389-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="b7389-237">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b7389-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="b7389-238">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="b7389-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="b7389-239">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="b7389-239">**Type**: `int`</span></span>  
<span data-ttu-id="b7389-240">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="b7389-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="b7389-241">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="b7389-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="b7389-242">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7389-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="b7389-243">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="b7389-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="b7389-244">Deshabilitación de la recarga de configuración de aplicaciones al realizar un cambio</span><span class="sxs-lookup"><span data-stu-id="b7389-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="b7389-245">[De forma predeterminada](xref:fundamentals/configuration/index#default), *appsettings.json* y *appsettings.{Environment}.json* se recargan cuando cambia el archivo.</span><span class="sxs-lookup"><span data-stu-id="b7389-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="b7389-246">Para deshabilitar este comportamiento de recarga en ASP.NET Core 5.0 o versiones posteriores, establezca la clave `hostBuilder:reloadConfigOnChange` en `false`.</span><span class="sxs-lookup"><span data-stu-id="b7389-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="b7389-247">**Clave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b7389-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="b7389-248">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-249">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="b7389-249">**Default**: `true`</span></span>  
<span data-ttu-id="b7389-250">**Argumento de la línea de comandos**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b7389-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="b7389-251">**Variable de entorno**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b7389-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="b7389-252">El separador de dos puntos (`:`) no funciona con las claves jerárquicas de variables de entorno en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="b7389-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="b7389-253">Para más información, consulte [Variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b7389-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="b7389-254">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="b7389-254">Settings for web apps</span></span>

<span data-ttu-id="b7389-255">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="b7389-256">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="b7389-257">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="b7389-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="b7389-258">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7389-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="b7389-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="b7389-259">CaptureStartupErrors</span></span>

<span data-ttu-id="b7389-260">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="b7389-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="b7389-261">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="b7389-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="b7389-262">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="b7389-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="b7389-263">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-264">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="b7389-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="b7389-265">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="b7389-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="b7389-266">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="b7389-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="b7389-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="b7389-267">DetailedErrors</span></span>

<span data-ttu-id="b7389-268">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="b7389-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="b7389-269">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="b7389-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="b7389-270">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-271">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="b7389-271">**Default**: `false`</span></span>  
<span data-ttu-id="b7389-272">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="b7389-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="b7389-273">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="b7389-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="b7389-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="b7389-275">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="b7389-276">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="b7389-277">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="b7389-278">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b7389-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="b7389-279">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-279">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-280">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="b7389-280">**Default**: Empty string</span></span>  
<span data-ttu-id="b7389-281">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b7389-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="b7389-282">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="b7389-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="b7389-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="b7389-284">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="b7389-285">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b7389-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="b7389-286">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-286">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-287">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="b7389-287">**Default**: Empty string</span></span>  
<span data-ttu-id="b7389-288">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b7389-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="b7389-289">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="b7389-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="b7389-290">HTTPS_Port</span></span>

<span data-ttu-id="b7389-291">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7389-291">The HTTPS redirect port.</span></span> <span data-ttu-id="b7389-292">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b7389-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b7389-293">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="b7389-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="b7389-294">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-294">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-295">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b7389-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="b7389-296">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="b7389-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="b7389-297">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="b7389-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="b7389-298">PreferHostingUrls</span></span>

<span data-ttu-id="b7389-299">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="b7389-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="b7389-300">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="b7389-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="b7389-301">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-302">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="b7389-302">**Default**: `true`</span></span>  
<span data-ttu-id="b7389-303">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="b7389-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="b7389-304">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="b7389-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="b7389-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="b7389-305">PreventHostingStartup</span></span>

<span data-ttu-id="b7389-306">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="b7389-307">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b7389-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="b7389-308">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="b7389-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="b7389-309">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-310">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="b7389-310">**Default**: `false`</span></span>  
<span data-ttu-id="b7389-311">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b7389-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="b7389-312">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="b7389-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="b7389-313">StartupAssembly</span></span>

<span data-ttu-id="b7389-314">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="b7389-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="b7389-315">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="b7389-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="b7389-316">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-316">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-317">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="b7389-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="b7389-318">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="b7389-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="b7389-319">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="b7389-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="b7389-320">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="b7389-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="b7389-321">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="b7389-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="b7389-322">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="b7389-322">URLs</span></span>

<span data-ttu-id="b7389-323">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b7389-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="b7389-324">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="b7389-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="b7389-325">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="b7389-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="b7389-326">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="b7389-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="b7389-327">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="b7389-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="b7389-328">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="b7389-328">**Key**: `urls`</span></span>  
<span data-ttu-id="b7389-329">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-329">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-330">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="b7389-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="b7389-331">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="b7389-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="b7389-332">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="b7389-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="b7389-333">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7389-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="b7389-334">Para obtener más información, vea <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="b7389-334">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>

### <a name="webroot"></a><span data-ttu-id="b7389-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-335">WebRoot</span></span>

<span data-ttu-id="b7389-336">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="b7389-337">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="b7389-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="b7389-338">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="b7389-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="b7389-339">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-339">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-340">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b7389-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="b7389-341">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b7389-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="b7389-342">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="b7389-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="b7389-343">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="b7389-344">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b7389-344">For more information, see:</span></span>

* [<span data-ttu-id="b7389-345">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="b7389-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="b7389-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="b7389-347">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="b7389-347">Manage the host lifetime</span></span>

<span data-ttu-id="b7389-348">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="b7389-349">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7389-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b7389-350">Run</span><span class="sxs-lookup"><span data-stu-id="b7389-350">Run</span></span>

<span data-ttu-id="b7389-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="b7389-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="b7389-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-352">RunAsync</span></span>

<span data-ttu-id="b7389-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="b7389-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="b7389-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-354">RunConsoleAsync</span></span>

<span data-ttu-id="b7389-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="b7389-356">Iniciar</span><span class="sxs-lookup"><span data-stu-id="b7389-356">Start</span></span>

<span data-ttu-id="b7389-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="b7389-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="b7389-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-358">StartAsync</span></span>

<span data-ttu-id="b7389-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="b7389-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="b7389-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b7389-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b7389-361">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="b7389-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="b7389-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-362">StopAsync</span></span>

<span data-ttu-id="b7389-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="b7389-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="b7389-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="b7389-364">WaitForShutdown</span></span>

<span data-ttu-id="b7389-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b7389-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="b7389-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="b7389-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="b7389-368">Control externo</span><span class="sxs-lookup"><span data-stu-id="b7389-368">External control</span></span>

<span data-ttu-id="b7389-369">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="b7389-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b7389-370">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="b7389-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="b7389-371">En este tema se proporciona información sobre cómo usar el host genérico de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7389-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="b7389-372">Para obtener información sobre cómo usar un host genérico de .NET en aplicaciones de consola, consulte [Host genérico de .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b7389-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="b7389-373">Definición de host</span><span class="sxs-lookup"><span data-stu-id="b7389-373">Host definition</span></span>

<span data-ttu-id="b7389-374">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="b7389-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="b7389-375">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="b7389-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="b7389-376">Registro</span><span class="sxs-lookup"><span data-stu-id="b7389-376">Logging</span></span>
* <span data-ttu-id="b7389-377">Configuración</span><span class="sxs-lookup"><span data-stu-id="b7389-377">Configuration</span></span>
* <span data-ttu-id="b7389-378">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="b7389-378">`IHostedService` implementations</span></span>

<span data-ttu-id="b7389-379">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7389-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="b7389-380">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="b7389-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="b7389-381">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b7389-382">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="b7389-382">Set up a host</span></span>

<span data-ttu-id="b7389-383">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="b7389-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="b7389-384">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-384">The `Main` method:</span></span>

* <span data-ttu-id="b7389-385">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="b7389-386">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="b7389-387">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host genérico:</span><span class="sxs-lookup"><span data-stu-id="b7389-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="b7389-388">En el código siguiente se crea un host genérico mediante una carga de trabajo que no es HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="b7389-389">La implementación de `IHostedService` se agrega al contenedor de DI:</span><span class="sxs-lookup"><span data-stu-id="b7389-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="b7389-390">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="b7389-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b7389-391">El código anterior se genera con las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7389-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="b7389-392">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="b7389-393">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="b7389-394">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="b7389-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="b7389-395">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="b7389-395">Default builder settings</span></span>

<span data-ttu-id="b7389-396">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="b7389-397">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span><span class="sxs-lookup"><span data-stu-id="b7389-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="b7389-398">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="b7389-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="b7389-399">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="b7389-400">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="b7389-400">Command-line arguments.</span></span>
* <span data-ttu-id="b7389-401">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="b7389-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="b7389-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="b7389-403">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="b7389-404">[Secretos del usuario](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="b7389-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="b7389-405">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-405">Environment variables.</span></span>
  * <span data-ttu-id="b7389-406">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="b7389-406">Command-line arguments.</span></span>
* <span data-ttu-id="b7389-407">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="b7389-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="b7389-408">Consola</span><span class="sxs-lookup"><span data-stu-id="b7389-408">Console</span></span>
  * <span data-ttu-id="b7389-409">Depuración</span><span class="sxs-lookup"><span data-stu-id="b7389-409">Debug</span></span>
  * <span data-ttu-id="b7389-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="b7389-410">EventSource</span></span>
  * <span data-ttu-id="b7389-411">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="b7389-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="b7389-412">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b7389-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="b7389-413">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="b7389-414">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="b7389-415">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="b7389-416">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="b7389-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="b7389-417">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7389-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="b7389-418">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="b7389-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="b7389-419">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="b7389-419">Enables IIS integration.</span></span> <span data-ttu-id="b7389-420">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="b7389-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="b7389-421">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="b7389-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b7389-422">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="b7389-422">Framework-provided services</span></span>

<span data-ttu-id="b7389-423">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="b7389-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="b7389-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="b7389-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="b7389-426">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b7389-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="b7389-427">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="b7389-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="b7389-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="b7389-429">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="b7389-430">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="b7389-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="b7389-431">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="b7389-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="b7389-432">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b7389-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="b7389-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-433">IHostLifetime</span></span>

<span data-ttu-id="b7389-434">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="b7389-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="b7389-435">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="b7389-435">The last implementation registered is used.</span></span>

<span data-ttu-id="b7389-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="b7389-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="b7389-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b7389-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="b7389-438">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="b7389-439">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b7389-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="b7389-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b7389-440">IHostEnvironment</span></span>

<span data-ttu-id="b7389-441">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="b7389-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="b7389-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b7389-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="b7389-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b7389-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="b7389-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="b7389-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="b7389-445">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="b7389-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b7389-446">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="b7389-446">Host configuration</span></span>

<span data-ttu-id="b7389-447">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="b7389-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="b7389-448">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="b7389-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="b7389-449">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="b7389-450">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="b7389-451">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-452">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b7389-453">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b7389-454">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="b7389-455">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b7389-456">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="b7389-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b7389-457">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="b7389-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="b7389-458">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="b7389-458">App configuration</span></span>

<span data-ttu-id="b7389-459">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7389-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b7389-460">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-461">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="b7389-462">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="b7389-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="b7389-463">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="b7389-464">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b7389-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="b7389-465">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="b7389-465">Settings for all app types</span></span>

<span data-ttu-id="b7389-466">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="b7389-467">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="b7389-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b7389-468">ApplicationName</span></span>

<span data-ttu-id="b7389-469">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="b7389-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="b7389-470">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="b7389-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b7389-471">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-471">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-472">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="b7389-473">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="b7389-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="b7389-474">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="b7389-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-475">ContentRoot</span></span>

<span data-ttu-id="b7389-476">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="b7389-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="b7389-477">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="b7389-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="b7389-478">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b7389-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b7389-479">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-479">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-480">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="b7389-481">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="b7389-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="b7389-482">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="b7389-483">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b7389-483">For more information, see:</span></span>

* [<span data-ttu-id="b7389-484">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="b7389-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="b7389-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="b7389-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b7389-486">EnvironmentName</span></span>

<span data-ttu-id="b7389-487">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="b7389-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="b7389-488">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="b7389-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b7389-489">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b7389-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="b7389-490">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="b7389-490">**Key**: `environment`</span></span>  
<span data-ttu-id="b7389-491">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-491">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-492">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="b7389-492">**Default**: `Production`</span></span>  
<span data-ttu-id="b7389-493">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="b7389-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="b7389-494">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="b7389-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="b7389-495">ShutdownTimeout</span></span>

<span data-ttu-id="b7389-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b7389-497">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="b7389-497">The default value is five seconds.</span></span>  <span data-ttu-id="b7389-498">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="b7389-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="b7389-499">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="b7389-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="b7389-500">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="b7389-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="b7389-501">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="b7389-502">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="b7389-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="b7389-503">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="b7389-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="b7389-504">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="b7389-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="b7389-505">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="b7389-505">**Type**: `int`</span></span>  
<span data-ttu-id="b7389-506">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="b7389-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="b7389-507">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="b7389-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="b7389-508">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7389-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="b7389-509">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="b7389-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="b7389-510">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="b7389-510">Settings for web apps</span></span>

<span data-ttu-id="b7389-511">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="b7389-512">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="b7389-513">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="b7389-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="b7389-514">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7389-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="b7389-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="b7389-515">CaptureStartupErrors</span></span>

<span data-ttu-id="b7389-516">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="b7389-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="b7389-517">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="b7389-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="b7389-518">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="b7389-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="b7389-519">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-520">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="b7389-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="b7389-521">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="b7389-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="b7389-522">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="b7389-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="b7389-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="b7389-523">DetailedErrors</span></span>

<span data-ttu-id="b7389-524">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="b7389-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="b7389-525">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="b7389-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="b7389-526">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-527">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="b7389-527">**Default**: `false`</span></span>  
<span data-ttu-id="b7389-528">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="b7389-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="b7389-529">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="b7389-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="b7389-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="b7389-531">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="b7389-532">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="b7389-533">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="b7389-534">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b7389-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="b7389-535">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-535">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-536">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="b7389-536">**Default**: Empty string</span></span>  
<span data-ttu-id="b7389-537">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b7389-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="b7389-538">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="b7389-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="b7389-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="b7389-540">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="b7389-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="b7389-541">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b7389-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="b7389-542">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-542">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-543">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="b7389-543">**Default**: Empty string</span></span>  
<span data-ttu-id="b7389-544">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b7389-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="b7389-545">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="b7389-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="b7389-546">HTTPS_Port</span></span>

<span data-ttu-id="b7389-547">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7389-547">The HTTPS redirect port.</span></span> <span data-ttu-id="b7389-548">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b7389-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b7389-549">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="b7389-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="b7389-550">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-550">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-551">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b7389-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="b7389-552">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="b7389-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="b7389-553">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="b7389-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="b7389-554">PreferHostingUrls</span></span>

<span data-ttu-id="b7389-555">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="b7389-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="b7389-556">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="b7389-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="b7389-557">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-558">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="b7389-558">**Default**: `true`</span></span>  
<span data-ttu-id="b7389-559">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="b7389-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="b7389-560">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="b7389-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="b7389-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="b7389-561">PreventHostingStartup</span></span>

<span data-ttu-id="b7389-562">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="b7389-563">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b7389-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="b7389-564">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="b7389-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="b7389-565">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="b7389-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b7389-566">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="b7389-566">**Default**: `false`</span></span>  
<span data-ttu-id="b7389-567">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b7389-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="b7389-568">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b7389-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="b7389-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="b7389-569">StartupAssembly</span></span>

<span data-ttu-id="b7389-570">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="b7389-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="b7389-571">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="b7389-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="b7389-572">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-572">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-573">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="b7389-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="b7389-574">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="b7389-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="b7389-575">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="b7389-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="b7389-576">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="b7389-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="b7389-577">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="b7389-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="b7389-578">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="b7389-578">URLs</span></span>

<span data-ttu-id="b7389-579">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b7389-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="b7389-580">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="b7389-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="b7389-581">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="b7389-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="b7389-582">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="b7389-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="b7389-583">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="b7389-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="b7389-584">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="b7389-584">**Key**: `urls`</span></span>  
<span data-ttu-id="b7389-585">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-585">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-586">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="b7389-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="b7389-587">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="b7389-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="b7389-588">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="b7389-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="b7389-589">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="b7389-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="b7389-590">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b7389-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="b7389-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-591">WebRoot</span></span>

<span data-ttu-id="b7389-592">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="b7389-593">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="b7389-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="b7389-594">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="b7389-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="b7389-595">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-595">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-596">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b7389-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="b7389-597">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b7389-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="b7389-598">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="b7389-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="b7389-599">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7389-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="b7389-600">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b7389-600">For more information, see:</span></span>

* [<span data-ttu-id="b7389-601">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="b7389-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="b7389-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="b7389-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="b7389-603">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="b7389-603">Manage the host lifetime</span></span>

<span data-ttu-id="b7389-604">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="b7389-605">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7389-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b7389-606">Run</span><span class="sxs-lookup"><span data-stu-id="b7389-606">Run</span></span>

<span data-ttu-id="b7389-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="b7389-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="b7389-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-608">RunAsync</span></span>

<span data-ttu-id="b7389-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="b7389-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="b7389-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-610">RunConsoleAsync</span></span>

<span data-ttu-id="b7389-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="b7389-612">Iniciar</span><span class="sxs-lookup"><span data-stu-id="b7389-612">Start</span></span>

<span data-ttu-id="b7389-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="b7389-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="b7389-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-614">StartAsync</span></span>

<span data-ttu-id="b7389-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="b7389-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="b7389-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b7389-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b7389-617">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="b7389-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="b7389-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-618">StopAsync</span></span>

<span data-ttu-id="b7389-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="b7389-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="b7389-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="b7389-620">WaitForShutdown</span></span>

<span data-ttu-id="b7389-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b7389-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="b7389-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="b7389-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="b7389-624">Control externo</span><span class="sxs-lookup"><span data-stu-id="b7389-624">External control</span></span>

<span data-ttu-id="b7389-625">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="b7389-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b7389-626">Las aplicaciones ASP.NET Core configuran e inician un host.</span><span class="sxs-lookup"><span data-stu-id="b7389-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="b7389-627">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="b7389-628">En este artículo se trata el host genérico de ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que se usa para hospedar aplicaciones que no procesan solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7389-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="b7389-629">El objetivo del host genérico es desacoplar la canalización HTTP de la API host web para habilitar una mayor variedad de escenarios de host.</span><span class="sxs-lookup"><span data-stu-id="b7389-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="b7389-630">La mensajería, las tareas en segundo plano y otras cargas de trabajo que no son de HTTP basadas en la ventaja de host genérico se benefician de funcionalidades transversales, como la configuración, la inserción de dependencias (DI) y el registro.</span><span class="sxs-lookup"><span data-stu-id="b7389-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="b7389-631">El host genérico es una novedad de ASP.NET Core 2.1 y no es adecuado para escenarios de hospedaje web.</span><span class="sxs-lookup"><span data-stu-id="b7389-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="b7389-632">Para escenarios de hospedaje web, use el [host web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b7389-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b7389-633">El host genérico reemplazará al host web en una próxima versión y actuará como la API de host principal tanto en escenarios de HTTP como los que no lo son.</span><span class="sxs-lookup"><span data-stu-id="b7389-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="b7389-634">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7389-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b7389-635">Al ejecutar la aplicación de ejemplo en [Visual Studio Code](https://code.visualstudio.com/), use un *terminal integrado o externo*.</span><span class="sxs-lookup"><span data-stu-id="b7389-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="b7389-636">No ejecute el ejemplo en `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="b7389-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="b7389-637">Para establecer la consola en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b7389-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="b7389-638">Abra el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="b7389-639">En la configuración de **.NET Core Launch (console)** , busque la entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="b7389-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="b7389-640">Establezca el valor en `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="b7389-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="b7389-641">Introducción</span><span class="sxs-lookup"><span data-stu-id="b7389-641">Introduction</span></span>

<span data-ttu-id="b7389-642">La biblioteca de host genérico está disponible en el espacio de nombres <xref:Microsoft.Extensions.Hosting> y la proporciona el paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="b7389-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="b7389-643">El paquete `Microsoft.Extensions.Hosting` está incluido en el metapaquete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o posterior).</span><span class="sxs-lookup"><span data-stu-id="b7389-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="b7389-644"><xref:Microsoft.Extensions.Hosting.IHostedService> es el punto de entrada para la ejecución de código.</span><span class="sxs-lookup"><span data-stu-id="b7389-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="b7389-645">Cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> se ejecuta en el orden de [registro del servicio en ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="b7389-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="b7389-646">Se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> en cada <xref:Microsoft.Extensions.Hosting.IHostedService> cuando se inicia el host, y se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> en orden inverso del registro cuando el host se cierra de forma estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b7389-647">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="b7389-647">Set up a host</span></span>

<span data-ttu-id="b7389-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> es el componente principal que usan las bibliotecas y aplicaciones para inicializar, compilar y ejecutar el host:</span><span class="sxs-lookup"><span data-stu-id="b7389-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="b7389-649">Opciones</span><span class="sxs-lookup"><span data-stu-id="b7389-649">Options</span></span>

<span data-ttu-id="b7389-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configura opciones para <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="b7389-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="b7389-651">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="b7389-651">Shutdown timeout</span></span>

<span data-ttu-id="b7389-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b7389-653">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="b7389-653">The default value is five seconds.</span></span>

<span data-ttu-id="b7389-654">La siguiente configuración de opción en `Program.Main` aumenta el tiempo de espera de apagado predeterminado de 5 segundos a 20:</span><span class="sxs-lookup"><span data-stu-id="b7389-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="b7389-655">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="b7389-655">Default services</span></span>

<span data-ttu-id="b7389-656">Estos son los servicios que se registran durante la inicialización del host:</span><span class="sxs-lookup"><span data-stu-id="b7389-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="b7389-657">[Entorno](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="b7389-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="b7389-658">[Configuración](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="b7389-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="b7389-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="b7389-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="b7389-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="b7389-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="b7389-661">[Opciones](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="b7389-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="b7389-662">[Registro](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="b7389-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b7389-663">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="b7389-663">Host configuration</span></span>

<span data-ttu-id="b7389-664">La configuración del host se crea:</span><span class="sxs-lookup"><span data-stu-id="b7389-664">Host configuration is created by:</span></span>

* <span data-ttu-id="b7389-665">Llamando a métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder> para establecer la [raíz del contenido](#content-root) y el [entorno](#environment).</span><span class="sxs-lookup"><span data-stu-id="b7389-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="b7389-666">Leyendo la configuración desde los proveedores de configuración de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="b7389-667">Métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="b7389-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="b7389-668">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="b7389-668">Application key (name)</span></span>

<span data-ttu-id="b7389-669">La propiedad [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) se establece desde la configuración del host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="b7389-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="b7389-670">Para establecer el valor explícitamente, use [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="b7389-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="b7389-671">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="b7389-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b7389-672">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-672">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-673">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="b7389-674">**Establecer mediante**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="b7389-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="b7389-675">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b7389-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="b7389-676">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="b7389-676">Content root</span></span>

<span data-ttu-id="b7389-677">Esta configuración determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="b7389-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="b7389-678">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b7389-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b7389-679">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-679">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-680">**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="b7389-681">**Establecer mediante**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="b7389-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="b7389-682">**Variable de entorno**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b7389-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="b7389-683">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="b7389-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="b7389-684">Para más información, consulte [Aspectos básicos: Raíz del contenido](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="b7389-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="b7389-685">Entorno</span><span class="sxs-lookup"><span data-stu-id="b7389-685">Environment</span></span>

<span data-ttu-id="b7389-686">Establece el [entorno](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b7389-687">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="b7389-687">**Key**: `environment`</span></span>  
<span data-ttu-id="b7389-688">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="b7389-688">**Type**: `string`</span></span>  
<span data-ttu-id="b7389-689">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="b7389-689">**Default**: `Production`</span></span>  
<span data-ttu-id="b7389-690">**Establecer mediante**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="b7389-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="b7389-691">**Variable de entorno**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b7389-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="b7389-692">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="b7389-692">The environment can be set to any value.</span></span> <span data-ttu-id="b7389-693">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="b7389-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b7389-694">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b7389-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="b7389-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="b7389-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="b7389-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para el host.</span><span class="sxs-lookup"><span data-stu-id="b7389-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="b7389-697">La configuración del host se usa para inicializar el <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para su uso en el proceso de compilación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="b7389-698">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-699">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b7389-700">De forma predeterminada no se incluye ningún proveedor.</span><span class="sxs-lookup"><span data-stu-id="b7389-700">No providers are included by default.</span></span> <span data-ttu-id="b7389-701">Debe especificar de forma explícita los proveedores de configuración que requiere la aplicación en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, así como:</span><span class="sxs-lookup"><span data-stu-id="b7389-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="b7389-702">La configuración de archivo (por ejemplo, de un archivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="b7389-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="b7389-703">La configuración de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-703">Environment variable configuration.</span></span>
* <span data-ttu-id="b7389-704">La configuración del argumento de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="b7389-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="b7389-705">Otros proveedores de configuración necesarios.</span><span class="sxs-lookup"><span data-stu-id="b7389-705">Any other required configuration providers.</span></span>

<span data-ttu-id="b7389-706">La configuración de archivo del host se habilita especificando la ruta base de la aplicación con `SetBasePath` seguido de una llamada a uno de los [proveedores de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b7389-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="b7389-707">La aplicación de ejemplo usa un archivo JSON, *hostsettings.json*, y llama a <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir los valores de configuración del host del archivo.</span><span class="sxs-lookup"><span data-stu-id="b7389-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="b7389-708">Para agregar una [configuración de la variable de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) del host, llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en el generador del host.</span><span class="sxs-lookup"><span data-stu-id="b7389-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="b7389-709">`AddEnvironmentVariables` acepta un prefijo opcional definido por el usuario.</span><span class="sxs-lookup"><span data-stu-id="b7389-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="b7389-710">La aplicación de ejemplo usa el prefijo `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="b7389-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="b7389-711">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b7389-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b7389-712">Al configurar el host de la aplicación de ejemplo, el valor de la variable de entorno de `PREFIX_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="b7389-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b7389-713">Durante el desarrollo, al usar [Visual Studio](https://visualstudio.microsoft.com) o al ejecutar una aplicación con `dotnet run`, se pueden establecer variables de entorno en el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b7389-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="b7389-714">En [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json* durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b7389-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="b7389-715">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b7389-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b7389-716">La [configuración de la línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) se agrega llamando a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="b7389-717">La configuración de la línea de comandos se agrega en último lugar para permitir que los argumentos de la línea de comandos invaliden la configuración proporcionada por los proveedores de configuración anteriores.</span><span class="sxs-lookup"><span data-stu-id="b7389-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="b7389-718">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b7389-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="b7389-719">Se puede proporcionar una configuración adicional con las claves [applicationName](#application-key-name) y [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="b7389-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="b7389-720">Configuración de `HostBuilder` de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b7389-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="b7389-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b7389-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b7389-722">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en la implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b7389-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="b7389-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="b7389-724">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="b7389-725">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="b7389-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="b7389-726">La configuración creada por <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y en <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="b7389-727">La configuración de la aplicación recibe automáticamente la configuración del host proporcionada por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b7389-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="b7389-728">Configuración de aplicación de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b7389-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="b7389-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b7389-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="b7389-730">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="b7389-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="b7389-731">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="b7389-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="b7389-732">Para mover los archivos de configuración al directorio de salida, especifique los archivos de configuración como [elementos de proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7389-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="b7389-733">La aplicación de ejemplo mueve sus archivos de configuración de aplicación JSON y *hostsettings.json* con el elemento `<Content>` siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7389-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="b7389-734">Los métodos de extensión de configuración, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> y <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> requieren paquetes NuGet adicionales, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) y [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="b7389-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="b7389-735">A menos que la aplicación use el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), además del paquete principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), se deben agregar estos paquetes.</span><span class="sxs-lookup"><span data-stu-id="b7389-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="b7389-736">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b7389-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="b7389-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="b7389-737">ConfigureServices</span></span>

<span data-ttu-id="b7389-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> agrega los servicios al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b7389-739">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="b7389-740">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="b7389-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b7389-741">Para obtener más información, vea <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b7389-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="b7389-742">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) usa el método de extensión `AddHostedService` para agregar un servicio para eventos de duración, `LifetimeEventsHostedService`, y una tarea en segundo plano programada, `TimedHostedService`, a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b7389-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="b7389-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="b7389-743">ConfigureLogging</span></span>

<span data-ttu-id="b7389-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> agrega un delegado para configurar el <xref:Microsoft.Extensions.Logging.ILoggingBuilder> proporcionado.</span><span class="sxs-lookup"><span data-stu-id="b7389-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="b7389-745">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="b7389-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-746">UseConsoleLifetime</span></span>

<span data-ttu-id="b7389-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="b7389-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b7389-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="b7389-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ya está registrado previamente como la implementación de duración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b7389-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="b7389-750">Se usa la última duración registrada.</span><span class="sxs-lookup"><span data-stu-id="b7389-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="b7389-751">Configuración del contenedor</span><span class="sxs-lookup"><span data-stu-id="b7389-751">Container configuration</span></span>

<span data-ttu-id="b7389-752">Para permitir la conexión a otros contenedores, el host puede aceptar <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="b7389-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="b7389-753">Proporcionar un generador no forma parte del registro de contenedor DI, sino que es un host intrínseco utilizado para crear el contenedor DI determinado.</span><span class="sxs-lookup"><span data-stu-id="b7389-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="b7389-754">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) invalida el generador predeterminado utilizado para crear el proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="b7389-755">La configuración personalizada del contenedor está administrada por el método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="b7389-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> proporciona una experiencia fuertemente tipada para configurar el contenedor sobre la API de host subyacente.</span><span class="sxs-lookup"><span data-stu-id="b7389-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="b7389-757">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="b7389-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="b7389-758">Crear un contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b7389-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="b7389-759">Proporcionar un generador de contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="b7389-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="b7389-760">Usar el generador y configurar el contenedor de servicio personalizado de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b7389-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="b7389-761">Extensibilidad</span><span class="sxs-lookup"><span data-stu-id="b7389-761">Extensibility</span></span>

<span data-ttu-id="b7389-762">La extensibilidad de host se realiza con métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b7389-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="b7389-763">En el ejemplo siguiente se muestra cómo un método de extensión extiende una implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder> con el ejemplo [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) demostrado en <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b7389-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="b7389-764">Una aplicación establece el método de extensión `UseHostedService` para registrar el servicio hospedado pasado en `T`:</span><span class="sxs-lookup"><span data-stu-id="b7389-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="b7389-765">Administración del host</span><span class="sxs-lookup"><span data-stu-id="b7389-765">Manage the host</span></span>

<span data-ttu-id="b7389-766">La implementación de <xref:Microsoft.Extensions.Hosting.IHost> es la responsable de iniciar y detener las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> que están registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b7389-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b7389-767">Run</span><span class="sxs-lookup"><span data-stu-id="b7389-767">Run</span></span>

<span data-ttu-id="b7389-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host:</span><span class="sxs-lookup"><span data-stu-id="b7389-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="b7389-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-769">RunAsync</span></span>

<span data-ttu-id="b7389-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre:</span><span class="sxs-lookup"><span data-stu-id="b7389-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="b7389-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-771">RunConsoleAsync</span></span>

<span data-ttu-id="b7389-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="b7389-773">Start y StopAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-773">Start and StopAsync</span></span>

<span data-ttu-id="b7389-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="b7389-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="b7389-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="b7389-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="b7389-776">StartAsync y StopAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="b7389-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="b7389-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> detiene la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="b7389-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="b7389-779">WaitForShutdown</span></span>

<span data-ttu-id="b7389-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se desencadena mediante <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="b7389-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="b7389-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="b7389-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="b7389-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="b7389-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b7389-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="b7389-784">Control externo</span><span class="sxs-lookup"><span data-stu-id="b7389-784">External control</span></span>

<span data-ttu-id="b7389-785">El control externo del host se puede lograr mediante métodos a los que se pueda llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="b7389-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b7389-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b7389-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b7389-787">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="b7389-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="b7389-788">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="b7389-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="b7389-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> proporciona información sobre el entorno de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="b7389-790">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="b7389-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="b7389-791">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b7389-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="b7389-792">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b7389-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="b7389-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite actividades posteriores al inicio y cierre, incluidas las solicitudes de cierre estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="b7389-794">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos <xref:System.Action> que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="b7389-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="b7389-795">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="b7389-795">Cancellation Token</span></span> | <span data-ttu-id="b7389-796">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="b7389-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="b7389-797">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="b7389-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="b7389-798">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="b7389-799">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b7389-799">All requests should be processed.</span></span> <span data-ttu-id="b7389-800">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="b7389-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="b7389-801">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="b7389-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="b7389-802">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b7389-802">Requests may still be processing.</span></span> <span data-ttu-id="b7389-803">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="b7389-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="b7389-804">Inserción de constructor del servicio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> en cualquier clase.</span><span class="sxs-lookup"><span data-stu-id="b7389-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="b7389-805">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) se usa la inserción de constructor en una clase `LifetimeEventsHostedService` (una implementación de <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar los eventos.</span><span class="sxs-lookup"><span data-stu-id="b7389-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="b7389-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7389-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="b7389-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7389-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="b7389-808">La siguiente clase usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="b7389-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b7389-809">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b7389-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
