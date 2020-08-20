---
title: Host genérico de .NET
author: rick-anderson
description: Obtenga información sobre el host genérico .NET Core, que es responsable de la administración de inicio y duración de la aplicación.
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
ms.openlocfilehash: e606812c1c2164a7e4d0926a76d2ff7ada4c9a87
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635390"
---
# <a name="net-generic-host"></a><span data-ttu-id="1d74e-103">Host genérico de .NET</span><span class="sxs-lookup"><span data-stu-id="1d74e-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="1d74e-104">Las plantillas de ASP.NET Core crean un host genérico de .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="1d74e-105">Definición de host</span><span class="sxs-lookup"><span data-stu-id="1d74e-105">Host definition</span></span>

<span data-ttu-id="1d74e-106">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="1d74e-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="1d74e-107">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="1d74e-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="1d74e-108">Registro</span><span class="sxs-lookup"><span data-stu-id="1d74e-108">Logging</span></span>
* <span data-ttu-id="1d74e-109">Configuración</span><span class="sxs-lookup"><span data-stu-id="1d74e-109">Configuration</span></span>
* <span data-ttu-id="1d74e-110">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="1d74e-110">`IHostedService` implementations</span></span>

<span data-ttu-id="1d74e-111">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="1d74e-112">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="1d74e-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="1d74e-113">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="1d74e-114">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="1d74e-114">Set up a host</span></span>

<span data-ttu-id="1d74e-115">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="1d74e-116">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-116">The `Main` method:</span></span>

* <span data-ttu-id="1d74e-117">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="1d74e-118">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="1d74e-119">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host genérico:</span><span class="sxs-lookup"><span data-stu-id="1d74e-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="1d74e-120">En el código siguiente se crea un host genérico mediante una carga de trabajo que no es HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="1d74e-121">La implementación de `IHostedService` se agrega al contenedor de DI:</span><span class="sxs-lookup"><span data-stu-id="1d74e-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="1d74e-122">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="1d74e-123">El código anterior se genera con las plantillas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1d74e-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="1d74e-124">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="1d74e-125">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="1d74e-126">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="1d74e-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="1d74e-127">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="1d74e-127">Default builder settings</span></span>

<span data-ttu-id="1d74e-128">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="1d74e-129">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="1d74e-130">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="1d74e-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="1d74e-131">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="1d74e-132">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-132">Command-line arguments.</span></span>
* <span data-ttu-id="1d74e-133">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="1d74e-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="1d74e-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="1d74e-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="1d74e-136">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="1d74e-137">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-137">Environment variables.</span></span>
  * <span data-ttu-id="1d74e-138">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-138">Command-line arguments.</span></span>
* <span data-ttu-id="1d74e-139">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="1d74e-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="1d74e-140">Consola</span><span class="sxs-lookup"><span data-stu-id="1d74e-140">Console</span></span>
  * <span data-ttu-id="1d74e-141">Depuración</span><span class="sxs-lookup"><span data-stu-id="1d74e-141">Debug</span></span>
  * <span data-ttu-id="1d74e-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="1d74e-142">EventSource</span></span>
  * <span data-ttu-id="1d74e-143">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="1d74e-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="1d74e-144">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="1d74e-145">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="1d74e-146">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="1d74e-147">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="1d74e-148">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="1d74e-149">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1d74e-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="1d74e-150">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="1d74e-151">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="1d74e-151">Enables IIS integration.</span></span> <span data-ttu-id="1d74e-152">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="1d74e-153">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="1d74e-154">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="1d74e-154">Framework-provided services</span></span>

<span data-ttu-id="1d74e-155">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="1d74e-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="1d74e-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="1d74e-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="1d74e-158">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="1d74e-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="1d74e-159">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="1d74e-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="1d74e-161">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="1d74e-162">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="1d74e-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="1d74e-163">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="1d74e-164">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="1d74e-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-165">IHostLifetime</span></span>

<span data-ttu-id="1d74e-166">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="1d74e-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="1d74e-167">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-167">The last implementation registered is used.</span></span>

<span data-ttu-id="1d74e-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="1d74e-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="1d74e-170">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="1d74e-171">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="1d74e-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="1d74e-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="1d74e-172">IHostEnvironment</span></span>

<span data-ttu-id="1d74e-173">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="1d74e-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="1d74e-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="1d74e-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="1d74e-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="1d74e-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="1d74e-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="1d74e-177">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="1d74e-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="1d74e-178">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="1d74e-178">Host configuration</span></span>

<span data-ttu-id="1d74e-179">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="1d74e-180">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="1d74e-181">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="1d74e-182">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="1d74e-183">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-184">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="1d74e-185">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1d74e-186">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="1d74e-187">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="1d74e-188">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="1d74e-189">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="1d74e-190">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="1d74e-190">App configuration</span></span>

<span data-ttu-id="1d74e-191">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="1d74e-192">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-193">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="1d74e-194">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="1d74e-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="1d74e-195">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="1d74e-196">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="1d74e-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="1d74e-197">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="1d74e-197">Settings for all app types</span></span>

<span data-ttu-id="1d74e-198">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="1d74e-199">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="1d74e-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="1d74e-200">ApplicationName</span></span>

<span data-ttu-id="1d74e-201">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="1d74e-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="1d74e-202">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="1d74e-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="1d74e-203">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-203">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-204">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="1d74e-205">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="1d74e-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="1d74e-206">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="1d74e-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-207">ContentRoot</span></span>

<span data-ttu-id="1d74e-208">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="1d74e-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="1d74e-209">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="1d74e-210">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="1d74e-211">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-211">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-212">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="1d74e-213">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="1d74e-214">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="1d74e-215">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1d74e-215">For more information, see:</span></span>

* [<span data-ttu-id="1d74e-216">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1d74e-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="1d74e-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="1d74e-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="1d74e-218">EnvironmentName</span></span>

<span data-ttu-id="1d74e-219">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="1d74e-220">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="1d74e-221">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1d74e-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="1d74e-222">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="1d74e-222">**Key**: `environment`</span></span>  
<span data-ttu-id="1d74e-223">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-223">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-224">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="1d74e-224">**Default**: `Production`</span></span>  
<span data-ttu-id="1d74e-225">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="1d74e-226">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="1d74e-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="1d74e-227">ShutdownTimeout</span></span>

<span data-ttu-id="1d74e-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="1d74e-229">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-229">The default value is five seconds.</span></span>  <span data-ttu-id="1d74e-230">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="1d74e-231">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="1d74e-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="1d74e-232">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="1d74e-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="1d74e-233">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="1d74e-234">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="1d74e-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="1d74e-235">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="1d74e-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="1d74e-236">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="1d74e-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="1d74e-237">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="1d74e-237">**Type**: `int`</span></span>  
<span data-ttu-id="1d74e-238">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="1d74e-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="1d74e-239">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="1d74e-240">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="1d74e-241">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="1d74e-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="1d74e-242">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="1d74e-242">Settings for web apps</span></span>

<span data-ttu-id="1d74e-243">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="1d74e-244">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="1d74e-245">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="1d74e-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="1d74e-246">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1d74e-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="1d74e-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="1d74e-247">CaptureStartupErrors</span></span>

<span data-ttu-id="1d74e-248">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="1d74e-249">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="1d74e-250">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="1d74e-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="1d74e-251">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-252">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="1d74e-253">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="1d74e-254">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="1d74e-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="1d74e-255">DetailedErrors</span></span>

<span data-ttu-id="1d74e-256">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="1d74e-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="1d74e-257">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="1d74e-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="1d74e-258">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-259">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="1d74e-259">**Default**: `false`</span></span>  
<span data-ttu-id="1d74e-260">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="1d74e-261">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="1d74e-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="1d74e-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="1d74e-263">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="1d74e-264">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="1d74e-265">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="1d74e-266">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="1d74e-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="1d74e-267">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-267">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-268">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1d74e-268">**Default**: Empty string</span></span>  
<span data-ttu-id="1d74e-269">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1d74e-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="1d74e-270">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="1d74e-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="1d74e-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="1d74e-272">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="1d74e-273">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="1d74e-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="1d74e-274">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-274">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-275">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1d74e-275">**Default**: Empty string</span></span>  
<span data-ttu-id="1d74e-276">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1d74e-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="1d74e-277">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="1d74e-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="1d74e-278">HTTPS_Port</span></span>

<span data-ttu-id="1d74e-279">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1d74e-279">The HTTPS redirect port.</span></span> <span data-ttu-id="1d74e-280">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1d74e-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1d74e-281">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="1d74e-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="1d74e-282">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-282">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-283">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="1d74e-284">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="1d74e-285">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="1d74e-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="1d74e-286">PreferHostingUrls</span></span>

<span data-ttu-id="1d74e-287">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="1d74e-288">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="1d74e-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="1d74e-289">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-290">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="1d74e-290">**Default**: `true`</span></span>  
<span data-ttu-id="1d74e-291">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="1d74e-292">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="1d74e-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="1d74e-293">PreventHostingStartup</span></span>

<span data-ttu-id="1d74e-294">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="1d74e-295">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="1d74e-296">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="1d74e-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="1d74e-297">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-298">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="1d74e-298">**Default**: `false`</span></span>  
<span data-ttu-id="1d74e-299">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="1d74e-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="1d74e-300">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="1d74e-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="1d74e-301">StartupAssembly</span></span>

<span data-ttu-id="1d74e-302">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="1d74e-303">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="1d74e-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="1d74e-304">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-304">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-305">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1d74e-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="1d74e-306">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="1d74e-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="1d74e-307">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="1d74e-308">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="1d74e-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="1d74e-309">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="1d74e-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="1d74e-310">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="1d74e-310">URLs</span></span>

<span data-ttu-id="1d74e-311">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1d74e-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="1d74e-312">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="1d74e-313">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="1d74e-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="1d74e-314">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="1d74e-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="1d74e-315">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="1d74e-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="1d74e-316">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="1d74e-316">**Key**: `urls`</span></span>  
<span data-ttu-id="1d74e-317">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-317">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-318">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="1d74e-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="1d74e-319">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="1d74e-320">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="1d74e-321">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="1d74e-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="1d74e-322">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="1d74e-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-323">WebRoot</span></span>

<span data-ttu-id="1d74e-324">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="1d74e-325">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="1d74e-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="1d74e-326">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="1d74e-327">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-327">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-328">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="1d74e-329">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="1d74e-330">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="1d74e-331">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="1d74e-332">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1d74e-332">For more information, see:</span></span>

* [<span data-ttu-id="1d74e-333">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="1d74e-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="1d74e-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="1d74e-335">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="1d74e-335">Manage the host lifetime</span></span>

<span data-ttu-id="1d74e-336">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="1d74e-337">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="1d74e-338">Run</span><span class="sxs-lookup"><span data-stu-id="1d74e-338">Run</span></span>

<span data-ttu-id="1d74e-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="1d74e-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-340">RunAsync</span></span>

<span data-ttu-id="1d74e-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="1d74e-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="1d74e-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-342">RunConsoleAsync</span></span>

<span data-ttu-id="1d74e-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="1d74e-344">Iniciar</span><span class="sxs-lookup"><span data-stu-id="1d74e-344">Start</span></span>

<span data-ttu-id="1d74e-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="1d74e-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="1d74e-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-346">StartAsync</span></span>

<span data-ttu-id="1d74e-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="1d74e-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="1d74e-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="1d74e-349">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="1d74e-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-350">StopAsync</span></span>

<span data-ttu-id="1d74e-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="1d74e-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="1d74e-352">WaitForShutdown</span></span>

<span data-ttu-id="1d74e-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="1d74e-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="1d74e-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="1d74e-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="1d74e-356">Control externo</span><span class="sxs-lookup"><span data-stu-id="1d74e-356">External control</span></span>

<span data-ttu-id="1d74e-357">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="1d74e-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="1d74e-358">Las aplicaciones ASP.NET Core configuran e inician un host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="1d74e-359">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="1d74e-360">En este artículo se trata el host genérico de ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que se usa para hospedar aplicaciones que no procesan solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="1d74e-361">El objetivo del host genérico es desacoplar la canalización HTTP de la API host web para habilitar una mayor variedad de escenarios de host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="1d74e-362">La mensajería, las tareas en segundo plano y otras cargas de trabajo que no son de HTTP basadas en la ventaja de host genérico se benefician de funcionalidades transversales, como la configuración, la inserción de dependencias (DI) y el registro.</span><span class="sxs-lookup"><span data-stu-id="1d74e-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="1d74e-363">El host genérico es una novedad de ASP.NET Core 2.1 y no es adecuado para escenarios de hospedaje web.</span><span class="sxs-lookup"><span data-stu-id="1d74e-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="1d74e-364">Para escenarios de hospedaje web, use el [host web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="1d74e-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="1d74e-365">El host genérico reemplazará al host web en una próxima versión y actuará como la API de host principal tanto en escenarios de HTTP como los que no lo son.</span><span class="sxs-lookup"><span data-stu-id="1d74e-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="1d74e-366">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d74e-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1d74e-367">Al ejecutar la aplicación de ejemplo en [Visual Studio Code](https://code.visualstudio.com/), use un *terminal integrado o externo*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="1d74e-368">No ejecute el ejemplo en `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="1d74e-369">Para establecer la consola en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="1d74e-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="1d74e-370">Abra el archivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="1d74e-371">En la configuración de **.NET Core Launch (console)** , busque la entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="1d74e-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="1d74e-372">Establezca el valor en `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="1d74e-373">Introducción</span><span class="sxs-lookup"><span data-stu-id="1d74e-373">Introduction</span></span>

<span data-ttu-id="1d74e-374">La biblioteca de host genérico está disponible en el espacio de nombres <xref:Microsoft.Extensions.Hosting> y la proporciona el paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="1d74e-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="1d74e-375">El paquete `Microsoft.Extensions.Hosting` está incluido en el metapaquete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o posterior).</span><span class="sxs-lookup"><span data-stu-id="1d74e-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="1d74e-376"><xref:Microsoft.Extensions.Hosting.IHostedService> es el punto de entrada para la ejecución de código.</span><span class="sxs-lookup"><span data-stu-id="1d74e-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="1d74e-377">Cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> se ejecuta en el orden de [registro del servicio en ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="1d74e-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="1d74e-378">Se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> en cada <xref:Microsoft.Extensions.Hosting.IHostedService> cuando se inicia el host, y se llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> en orden inverso del registro cuando el host se cierra de forma estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="1d74e-379">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="1d74e-379">Set up a host</span></span>

<span data-ttu-id="1d74e-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> es el componente principal que usan las bibliotecas y aplicaciones para inicializar, compilar y ejecutar el host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="1d74e-381">Opciones</span><span class="sxs-lookup"><span data-stu-id="1d74e-381">Options</span></span>

<span data-ttu-id="1d74e-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configura opciones para <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="1d74e-383">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="1d74e-383">Shutdown timeout</span></span>

<span data-ttu-id="1d74e-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="1d74e-385">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-385">The default value is five seconds.</span></span>

<span data-ttu-id="1d74e-386">La siguiente configuración de opción en `Program.Main` aumenta el tiempo de espera de apagado predeterminado de 5 segundos a 20:</span><span class="sxs-lookup"><span data-stu-id="1d74e-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="1d74e-387">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="1d74e-387">Default services</span></span>

<span data-ttu-id="1d74e-388">Estos son los servicios que se registran durante la inicialización del host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="1d74e-389">[Entorno](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="1d74e-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="1d74e-390">[Configuración](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="1d74e-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="1d74e-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="1d74e-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="1d74e-393">[Opciones](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="1d74e-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="1d74e-394">[Registro](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="1d74e-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="1d74e-395">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="1d74e-395">Host configuration</span></span>

<span data-ttu-id="1d74e-396">La configuración del host se crea:</span><span class="sxs-lookup"><span data-stu-id="1d74e-396">Host configuration is created by:</span></span>

* <span data-ttu-id="1d74e-397">Llamando a métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder> para establecer la [raíz del contenido](#content-root) y el [entorno](#environment).</span><span class="sxs-lookup"><span data-stu-id="1d74e-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="1d74e-398">Leyendo la configuración desde los proveedores de configuración de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="1d74e-399">Métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="1d74e-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="1d74e-400">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="1d74e-400">Application key (name)</span></span>

<span data-ttu-id="1d74e-401">La propiedad [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) se establece desde la configuración del host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="1d74e-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="1d74e-402">Para establecer el valor explícitamente, use [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="1d74e-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="1d74e-403">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="1d74e-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="1d74e-404">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-404">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-405">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="1d74e-406">**Establecer mediante**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="1d74e-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="1d74e-407">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="1d74e-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="1d74e-408">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1d74e-408">Content root</span></span>

<span data-ttu-id="1d74e-409">Esta configuración determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="1d74e-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="1d74e-410">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="1d74e-411">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-411">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-412">**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="1d74e-413">**Establecer mediante**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="1d74e-414">**Variable de entorno**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="1d74e-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="1d74e-415">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="1d74e-416">Para más información, consulte [Aspectos básicos: Raíz del contenido](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="1d74e-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="1d74e-417">Entorno</span><span class="sxs-lookup"><span data-stu-id="1d74e-417">Environment</span></span>

<span data-ttu-id="1d74e-418">Establece el [entorno](xref:fundamentals/environments) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="1d74e-419">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="1d74e-419">**Key**: `environment`</span></span>  
<span data-ttu-id="1d74e-420">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-420">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-421">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="1d74e-421">**Default**: `Production`</span></span>  
<span data-ttu-id="1d74e-422">**Establecer mediante**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="1d74e-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="1d74e-423">**Variable de entorno**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` es [opcional y definido por el usuario](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="1d74e-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="1d74e-424">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-424">The environment can be set to any value.</span></span> <span data-ttu-id="1d74e-425">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="1d74e-426">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1d74e-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="1d74e-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="1d74e-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="1d74e-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para el host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="1d74e-429">La configuración del host se usa para inicializar el <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para su uso en el proceso de compilación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="1d74e-430">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-431">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="1d74e-432">De forma predeterminada no se incluye ningún proveedor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-432">No providers are included by default.</span></span> <span data-ttu-id="1d74e-433">Debe especificar de forma explícita los proveedores de configuración que requiere la aplicación en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, así como:</span><span class="sxs-lookup"><span data-stu-id="1d74e-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="1d74e-434">La configuración de archivo (por ejemplo, de un archivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="1d74e-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="1d74e-435">La configuración de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-435">Environment variable configuration.</span></span>
* <span data-ttu-id="1d74e-436">La configuración del argumento de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="1d74e-437">Otros proveedores de configuración necesarios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-437">Any other required configuration providers.</span></span>

<span data-ttu-id="1d74e-438">La configuración de archivo del host se habilita especificando la ruta base de la aplicación con `SetBasePath` seguido de una llamada a uno de los [proveedores de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="1d74e-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="1d74e-439">La aplicación de ejemplo usa un archivo JSON, *hostsettings.json*, y llama a <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir los valores de configuración del host del archivo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="1d74e-440">Para agregar una [configuración de la variable de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) del host, llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en el generador del host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="1d74e-441">`AddEnvironmentVariables` acepta un prefijo opcional definido por el usuario.</span><span class="sxs-lookup"><span data-stu-id="1d74e-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="1d74e-442">La aplicación de ejemplo usa el prefijo `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="1d74e-443">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="1d74e-444">Al configurar el host de la aplicación de ejemplo, el valor de la variable de entorno de `PREFIX_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="1d74e-445">Durante el desarrollo, al usar [Visual Studio](https://visualstudio.microsoft.com) o al ejecutar una aplicación con `dotnet run`, se pueden establecer variables de entorno en el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="1d74e-446">En [Visual Studio Code](https://code.visualstudio.com/), las variables de entorno se pueden establecer en el archivo *.vscode/launch.json* durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="1d74e-447">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="1d74e-448">La [configuración de la línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) se agrega llamando a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="1d74e-449">La configuración de la línea de comandos se agrega en último lugar para permitir que los argumentos de la línea de comandos invaliden la configuración proporcionada por los proveedores de configuración anteriores.</span><span class="sxs-lookup"><span data-stu-id="1d74e-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="1d74e-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1d74e-450">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="1d74e-451">Se puede proporcionar una configuración adicional con las claves [applicationName](#application-key-name) y [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="1d74e-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="1d74e-452">Configuración de `HostBuilder` de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="1d74e-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="1d74e-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="1d74e-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="1d74e-454">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en la implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="1d74e-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para crear una <xref:Microsoft.Extensions.Configuration.IConfiguration> para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="1d74e-456">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-457">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="1d74e-458">La configuración creada por <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y en <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="1d74e-459">La configuración de la aplicación recibe automáticamente la configuración del host proporcionada por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="1d74e-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="1d74e-460">Configuración de aplicación de ejemplo con <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="1d74e-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="1d74e-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1d74e-461">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="1d74e-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="1d74e-462">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="1d74e-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="1d74e-463">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="1d74e-464">Para mover los archivos de configuración al directorio de salida, especifique los archivos de configuración como [elementos de proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) en el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1d74e-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="1d74e-465">La aplicación de ejemplo mueve sus archivos de configuración de aplicación JSON y *hostsettings.json* con el elemento `<Content>` siguiente:</span><span class="sxs-lookup"><span data-stu-id="1d74e-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="1d74e-466">Los métodos de extensión de configuración, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> y <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> requieren paquetes NuGet adicionales, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) y [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="1d74e-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="1d74e-467">A menos que la aplicación use el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), además del paquete principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), se deben agregar estos paquetes.</span><span class="sxs-lookup"><span data-stu-id="1d74e-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="1d74e-468">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="1d74e-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="1d74e-469">ConfigureServices</span></span>

<span data-ttu-id="1d74e-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> agrega los servicios al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1d74e-471">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="1d74e-472">Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1d74e-473">Para obtener más información, vea <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="1d74e-474">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa el método de extensión `AddHostedService` para agregar un servicio para eventos de duración, `LifetimeEventsHostedService`, y una tarea en segundo plano programada, `TimedHostedService`, a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1d74e-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="1d74e-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="1d74e-475">ConfigureLogging</span></span>

<span data-ttu-id="1d74e-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> agrega un delegado para configurar el <xref:Microsoft.Extensions.Logging.ILoggingBuilder> proporcionado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="1d74e-477">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="1d74e-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-478">UseConsoleLifetime</span></span>

<span data-ttu-id="1d74e-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="1d74e-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="1d74e-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="1d74e-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ya está registrado previamente como la implementación de duración predeterminada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="1d74e-482">Se usa la última duración registrada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="1d74e-483">Configuración del contenedor</span><span class="sxs-lookup"><span data-stu-id="1d74e-483">Container configuration</span></span>

<span data-ttu-id="1d74e-484">Para permitir la conexión a otros contenedores, el host puede aceptar <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="1d74e-485">Proporcionar un generador no forma parte del registro de contenedor DI, sino que es un host intrínseco utilizado para crear el contenedor DI determinado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="1d74e-486">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) invalida el generador predeterminado utilizado para crear el proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="1d74e-487">La configuración personalizada del contenedor está administrada por el método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="1d74e-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> proporciona una experiencia fuertemente tipada para configurar el contenedor sobre la API de host subyacente.</span><span class="sxs-lookup"><span data-stu-id="1d74e-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="1d74e-489">Se puede llamar varias veces a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="1d74e-490">Crear un contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1d74e-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="1d74e-491">Proporcionar un generador de contenedor de servicio:</span><span class="sxs-lookup"><span data-stu-id="1d74e-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="1d74e-492">Usar el generador y configurar el contenedor de servicio personalizado de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1d74e-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="1d74e-493">Extensibilidad</span><span class="sxs-lookup"><span data-stu-id="1d74e-493">Extensibility</span></span>

<span data-ttu-id="1d74e-494">La extensibilidad de host se realiza con métodos de extensión en <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="1d74e-495">En el ejemplo siguiente se muestra cómo un método de extensión extiende una implementación de <xref:Microsoft.Extensions.Hosting.IHostBuilder> con el ejemplo [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) demostrado en <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="1d74e-496">Una aplicación establece el método de extensión `UseHostedService` para registrar el servicio hospedado pasado en `T`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="1d74e-497">Administración del host</span><span class="sxs-lookup"><span data-stu-id="1d74e-497">Manage the host</span></span>

<span data-ttu-id="1d74e-498">La implementación de <xref:Microsoft.Extensions.Hosting.IHost> es la responsable de iniciar y detener las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> que están registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="1d74e-499">Run</span><span class="sxs-lookup"><span data-stu-id="1d74e-499">Run</span></span>

<span data-ttu-id="1d74e-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="1d74e-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-501">RunAsync</span></span>

<span data-ttu-id="1d74e-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre:</span><span class="sxs-lookup"><span data-stu-id="1d74e-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="1d74e-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-503">RunConsoleAsync</span></span>

<span data-ttu-id="1d74e-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="1d74e-505">Start y StopAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-505">Start and StopAsync</span></span>

<span data-ttu-id="1d74e-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="1d74e-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="1d74e-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="1d74e-508">StartAsync y StopAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="1d74e-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="1d74e-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> detiene la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="1d74e-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="1d74e-511">WaitForShutdown</span></span>

<span data-ttu-id="1d74e-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se desencadena mediante <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="1d74e-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="1d74e-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="1d74e-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="1d74e-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="1d74e-516">Control externo</span><span class="sxs-lookup"><span data-stu-id="1d74e-516">External control</span></span>

<span data-ttu-id="1d74e-517">El control externo del host se puede lograr mediante métodos a los que se pueda llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="1d74e-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="1d74e-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="1d74e-519">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="1d74e-520">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="1d74e-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="1d74e-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> proporciona información sobre el entorno de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="1d74e-522">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="1d74e-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="1d74e-523">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="1d74e-524">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="1d74e-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite actividades posteriores al inicio y cierre, incluidas las solicitudes de cierre estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="1d74e-526">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos <xref:System.Action> que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="1d74e-527">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="1d74e-527">Cancellation Token</span></span> | <span data-ttu-id="1d74e-528">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="1d74e-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="1d74e-529">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="1d74e-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="1d74e-530">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="1d74e-531">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1d74e-531">All requests should be processed.</span></span> <span data-ttu-id="1d74e-532">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1d74e-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="1d74e-533">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="1d74e-534">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1d74e-534">Requests may still be processing.</span></span> <span data-ttu-id="1d74e-535">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1d74e-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="1d74e-536">Inserción de constructor del servicio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> en cualquier clase.</span><span class="sxs-lookup"><span data-stu-id="1d74e-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="1d74e-537">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) se usa la inserción de constructor en una clase `LifetimeEventsHostedService` (una implementación de <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar los eventos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="1d74e-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d74e-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="1d74e-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="1d74e-540">La siguiente clase usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="1d74e-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="1d74e-541">Las plantillas de ASP.NET Core crean un host genérico de .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="1d74e-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="1d74e-542">Definición de host</span><span class="sxs-lookup"><span data-stu-id="1d74e-542">Host definition</span></span>

<span data-ttu-id="1d74e-543">El *host* es un objeto que encapsula todos los recursos de la aplicación, como:</span><span class="sxs-lookup"><span data-stu-id="1d74e-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="1d74e-544">Inserción de dependencias (ID)</span><span class="sxs-lookup"><span data-stu-id="1d74e-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="1d74e-545">Registro</span><span class="sxs-lookup"><span data-stu-id="1d74e-545">Logging</span></span>
* <span data-ttu-id="1d74e-546">Configuración</span><span class="sxs-lookup"><span data-stu-id="1d74e-546">Configuration</span></span>
* <span data-ttu-id="1d74e-547">Implementaciones de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="1d74e-547">`IHostedService` implementations</span></span>

<span data-ttu-id="1d74e-548">Cuando se inicia un host, llama a <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> en cada implementación de <xref:Microsoft.Extensions.Hosting.IHostedService> registrada en la colección de servicios hospedados del contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="1d74e-549">En una aplicación web, una de las implementaciones de `IHostedService` es un servicio web que inicia una [implementación de servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="1d74e-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="1d74e-550">La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="1d74e-551">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="1d74e-551">Set up a host</span></span>

<span data-ttu-id="1d74e-552">Normalmente se configura, compila y ejecuta el host por el código de la clase `Program`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="1d74e-553">El método `Main` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-553">The `Main` method:</span></span>

* <span data-ttu-id="1d74e-554">Llama a un método `CreateHostBuilder` para crear y configurar un objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="1d74e-555">Llama a los métodos `Build` y `Run` en el objeto del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="1d74e-556">Las plantillas web de ASP.NET Core generan el código siguiente para crear un host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="1d74e-557">En el código siguiente se crea una carga de trabajo que no es HTTP con una implementación de `IHostedService` agregada al contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="1d74e-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="1d74e-558">Para una carga de trabajo HTTP, el método `Main` es el mismo, pero `CreateHostBuilder` llama a `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="1d74e-559">Si la aplicación usa Entity Framework Core, no cambie el nombre o la firma del método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="1d74e-560">Las [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/) esperan encontrar un método `CreateHostBuilder` que configure el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="1d74e-561">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="1d74e-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="1d74e-562">Configuración predeterminada del generador</span><span class="sxs-lookup"><span data-stu-id="1d74e-562">Default builder settings</span></span>

<span data-ttu-id="1d74e-563">El método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="1d74e-564">Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="1d74e-565">Carga la configuración de host de:</span><span class="sxs-lookup"><span data-stu-id="1d74e-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="1d74e-566">Variables de entorno con el prefijo `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="1d74e-567">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-567">Command-line arguments.</span></span>
* <span data-ttu-id="1d74e-568">Carga la configuración de aplicación de:</span><span class="sxs-lookup"><span data-stu-id="1d74e-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="1d74e-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="1d74e-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="1d74e-571">[Administrador de secretos](xref:security/app-secrets), cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="1d74e-572">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-572">Environment variables.</span></span>
  * <span data-ttu-id="1d74e-573">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-573">Command-line arguments.</span></span>
* <span data-ttu-id="1d74e-574">Agrega los siguientes proveedores de [registro](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="1d74e-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="1d74e-575">Consola</span><span class="sxs-lookup"><span data-stu-id="1d74e-575">Console</span></span>
  * <span data-ttu-id="1d74e-576">Depuración</span><span class="sxs-lookup"><span data-stu-id="1d74e-576">Debug</span></span>
  * <span data-ttu-id="1d74e-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="1d74e-577">EventSource</span></span>
  * <span data-ttu-id="1d74e-578">EventLog (solo si se ejecuta en Windows)</span><span class="sxs-lookup"><span data-stu-id="1d74e-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="1d74e-579">Permite la [validación del ámbito](xref:fundamentals/dependency-injection#scope-validation) y la [validación de dependencias](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) si el entorno es Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="1d74e-580">El método `ConfigureWebHostDefaults` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="1d74e-581">Carga la configuración de host desde las variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="1d74e-582">Establece el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web y lo configura por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="1d74e-583">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="1d74e-584">Agrega el [middleware de filtrado de hosts](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="1d74e-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="1d74e-585">Agrega [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` es igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="1d74e-586">Permite la integración de IIS.</span><span class="sxs-lookup"><span data-stu-id="1d74e-586">Enables IIS integration.</span></span> <span data-ttu-id="1d74e-587">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="1d74e-588">En las secciones [Configuración para todos los tipos de aplicaciones](#settings-for-all-app-types) y [Configuración para las aplicaciones web](#settings-for-web-apps), más adelante en este artículo, se muestra cómo invalidar la configuración predeterminada del generador.</span><span class="sxs-lookup"><span data-stu-id="1d74e-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="1d74e-589">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="1d74e-589">Framework-provided services</span></span>

<span data-ttu-id="1d74e-590">Los servicios siguientes se registran de forma automática:</span><span class="sxs-lookup"><span data-stu-id="1d74e-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="1d74e-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="1d74e-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="1d74e-593">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="1d74e-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="1d74e-594">Para más información sobre los servicios proporcionados por el marco, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="1d74e-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="1d74e-596">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente, `IApplicationLifetime`) en cualquier clase para controlar las tareas posteriores al inicio y el cierre estable.</span><span class="sxs-lookup"><span data-stu-id="1d74e-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="1d74e-597">Tres de las propiedades de la interfaz son tokens de cancelación que se usan para registrar los métodos del controlador de eventos de inicio y detención de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="1d74e-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="1d74e-598">La interfaz también incluye un método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="1d74e-599">El ejemplo siguiente es una implementación de `IHostedService` que registra los eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="1d74e-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="1d74e-600">IHostLifetime</span></span>

<span data-ttu-id="1d74e-601">La implementación de <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla cuándo se inicia el host y cuándo se detiene.</span><span class="sxs-lookup"><span data-stu-id="1d74e-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="1d74e-602">Se usa la última implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-602">The last implementation registered is used.</span></span>

<span data-ttu-id="1d74e-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` es la implementación predeterminada de `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="1d74e-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="1d74e-605">escucha <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM, y llama a <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar el proceso de apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="1d74e-606">Desbloquea extensiones como [RunAsync](#runasync) y [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="1d74e-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="1d74e-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="1d74e-607">IHostEnvironment</span></span>

<span data-ttu-id="1d74e-608">Permite insertar el servicio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> en una clase para obtener información sobre los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="1d74e-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="1d74e-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="1d74e-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="1d74e-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="1d74e-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="1d74e-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="1d74e-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="1d74e-612">Las aplicaciones web implementan la interfaz `IWebHostEnvironment`, que hereda `IHostEnvironment` y agrega [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="1d74e-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="1d74e-613">Configuración de host</span><span class="sxs-lookup"><span data-stu-id="1d74e-613">Host configuration</span></span>

<span data-ttu-id="1d74e-614">La configuración de host se usa para las propiedades de la implementación de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="1d74e-615">La configuración de host está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration), en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="1d74e-616">Después de `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` se reemplaza por la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="1d74e-617">Para agregar la configuración de host, llame a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="1d74e-618">Se puede llamar varias veces a `ConfigureHostConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-619">El host usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="1d74e-620">El proveedor de variables de entorno con el prefijo `DOTNET_` y los argumentos de línea de comandos se incluyen mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1d74e-621">Para las aplicaciones web, se agrega el proveedor de variables de entorno con el prefijo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="1d74e-622">El prefijo se quita cuando se leen las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="1d74e-623">Por ejemplo, el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` se convierte en el valor de configuración de host de la clave `environment`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="1d74e-624">En el ejemplo siguiente se crea la configuración de host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="1d74e-625">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="1d74e-625">App configuration</span></span>

<span data-ttu-id="1d74e-626">La configuración de la aplicación se crea llamando a <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> en `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="1d74e-627">Se puede llamar varias veces a `ConfigureAppConfiguration` con resultados de suma.</span><span class="sxs-lookup"><span data-stu-id="1d74e-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="1d74e-628">La aplicación usa cualquier opción que establezca un valor en último lugar en una clave determinada.</span><span class="sxs-lookup"><span data-stu-id="1d74e-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="1d74e-629">La configuración creada por `ConfigureAppConfiguration` está disponible en [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para las operaciones posteriores y como servicio de ID.</span><span class="sxs-lookup"><span data-stu-id="1d74e-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="1d74e-630">La configuración de host también se agrega a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="1d74e-631">Para más información, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="1d74e-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="1d74e-632">Configuración para todos los tipos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="1d74e-632">Settings for all app types</span></span>

<span data-ttu-id="1d74e-633">En esta sección se enumeran las configuraciones de host que se aplican a las cargas de trabajo HTTP y no HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="1d74e-634">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="1d74e-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="1d74e-635">ApplicationName</span></span>

<span data-ttu-id="1d74e-636">La propiedad [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) se establece desde la configuración de host durante la construcción de este.</span><span class="sxs-lookup"><span data-stu-id="1d74e-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="1d74e-637">**Clave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="1d74e-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="1d74e-638">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-638">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-639">**Predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="1d74e-640">**Variable de entorno**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="1d74e-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="1d74e-641">Para establecer este valor, use la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="1d74e-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="1d74e-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-642">ContentRoot</span></span>

<span data-ttu-id="1d74e-643">La propiedad [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina la ubicación en la que el host comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="1d74e-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="1d74e-644">Si no existe la ruta de acceso, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="1d74e-645">**Clave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="1d74e-646">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-646">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-647">**Predeterminado**: carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="1d74e-648">**Variable de entorno**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="1d74e-649">Para establecer este valor, use la variable de entorno o llame a `UseContentRoot` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="1d74e-650">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1d74e-650">For more information, see:</span></span>

* [<span data-ttu-id="1d74e-651">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1d74e-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="1d74e-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="1d74e-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="1d74e-653">EnvironmentName</span></span>

<span data-ttu-id="1d74e-654">La propiedad [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) puede establecerse en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="1d74e-655">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="1d74e-656">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1d74e-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="1d74e-657">**Clave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="1d74e-657">**Key**: `environment`</span></span>  
<span data-ttu-id="1d74e-658">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-658">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-659">**Predeterminado**: `Production`</span><span class="sxs-lookup"><span data-stu-id="1d74e-659">**Default**: `Production`</span></span>  
<span data-ttu-id="1d74e-660">**Variable de entorno**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="1d74e-661">Para establecer este valor, use la variable de entorno o llame a `UseEnvironment` en `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="1d74e-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="1d74e-662">ShutdownTimeout</span></span>

<span data-ttu-id="1d74e-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) establece el tiempo de espera para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="1d74e-664">El valor predeterminado es cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="1d74e-664">The default value is five seconds.</span></span>  <span data-ttu-id="1d74e-665">Durante el período de tiempo de espera, el host:</span><span class="sxs-lookup"><span data-stu-id="1d74e-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="1d74e-666">Activa [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="1d74e-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="1d74e-667">Trata de detener los servicios hospedados y registra los errores que se producen en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="1d74e-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="1d74e-668">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="1d74e-669">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="1d74e-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="1d74e-670">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="1d74e-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="1d74e-671">**Clave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="1d74e-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="1d74e-672">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="1d74e-672">**Type**: `int`</span></span>  
<span data-ttu-id="1d74e-673">**Predeterminado**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="1d74e-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="1d74e-674">**Variable de entorno**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="1d74e-675">Para establecer este valor, use la variable de entorno o configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="1d74e-676">El siguiente ejemplo establece el tiempo de espera en 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="1d74e-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="1d74e-677">Deshabilitación de la recarga de configuración de aplicaciones al realizar un cambio</span><span class="sxs-lookup"><span data-stu-id="1d74e-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="1d74e-678">[De forma predeterminada](xref:fundamentals/configuration/index#default), *appsettings.json* y *appsettings.{Entorno}.json* se recargan cuando el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="1d74e-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="1d74e-679">Para deshabilitar este comportamiento de recarga en ASP.NET Core 5.0 Preview 3 o versiones posteriores, establezca la clave `hostBuilder:reloadConfigOnChange` en `false`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="1d74e-680">**Clave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="1d74e-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="1d74e-681">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-682">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="1d74e-682">**Default**: `true`</span></span>  
<span data-ttu-id="1d74e-683">**Argumento de la línea de comandos**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="1d74e-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="1d74e-684">**Variable de entorno**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="1d74e-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="1d74e-685">El separador de dos puntos (`:`) no funciona con las claves jerárquicas de variables de entorno en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="1d74e-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="1d74e-686">Para más información, consulte [Variables de entorno](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="1d74e-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="1d74e-687">Configuración para las aplicaciones web</span><span class="sxs-lookup"><span data-stu-id="1d74e-687">Settings for web apps</span></span>

<span data-ttu-id="1d74e-688">Algunas configuraciones de host solo se aplican a las cargas de trabajo HTTP.</span><span class="sxs-lookup"><span data-stu-id="1d74e-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="1d74e-689">De forma predeterminada, las variables de entorno que se usan para configurar estas opciones pueden tener un prefijo `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="1d74e-690">Los métodos de extensión en `IWebHostBuilder` están disponibles para estas configuraciones.</span><span class="sxs-lookup"><span data-stu-id="1d74e-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="1d74e-691">Los ejemplos de código que muestran cómo llamar a los métodos de extensión suponen que `webBuilder` es una instancia de `IWebHostBuilder`, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1d74e-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="1d74e-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="1d74e-692">CaptureStartupErrors</span></span>

<span data-ttu-id="1d74e-693">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="1d74e-694">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="1d74e-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="1d74e-695">**Clave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="1d74e-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="1d74e-696">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-697">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="1d74e-698">**Variable de entorno**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="1d74e-699">Para establecer este valor, utilice la configuración o llame a `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="1d74e-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="1d74e-700">DetailedErrors</span></span>

<span data-ttu-id="1d74e-701">Si se habilita, o si el entorno es `Development`, la aplicación captura errores detallados.</span><span class="sxs-lookup"><span data-stu-id="1d74e-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="1d74e-702">**Clave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="1d74e-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="1d74e-703">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-704">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="1d74e-704">**Default**: `false`</span></span>  
<span data-ttu-id="1d74e-705">**Variable de entorno**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="1d74e-706">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="1d74e-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="1d74e-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="1d74e-708">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="1d74e-709">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="1d74e-710">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="1d74e-711">**Clave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="1d74e-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="1d74e-712">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-712">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-713">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1d74e-713">**Default**: Empty string</span></span>  
<span data-ttu-id="1d74e-714">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1d74e-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="1d74e-715">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="1d74e-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="1d74e-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="1d74e-717">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1d74e-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="1d74e-718">**Clave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="1d74e-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="1d74e-719">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-719">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-720">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1d74e-720">**Default**: Empty string</span></span>  
<span data-ttu-id="1d74e-721">**Variable de entorno**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1d74e-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="1d74e-722">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="1d74e-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="1d74e-723">HTTPS_Port</span></span>

<span data-ttu-id="1d74e-724">Puerto de redireccionamiento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1d74e-724">The HTTPS redirect port.</span></span> <span data-ttu-id="1d74e-725">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1d74e-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1d74e-726">**Clave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="1d74e-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="1d74e-727">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-727">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-728">**Predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="1d74e-729">**Variable de entorno**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="1d74e-730">Para establecer este valor, utilice la configuración o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="1d74e-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="1d74e-731">PreferHostingUrls</span></span>

<span data-ttu-id="1d74e-732">Indica si el host debe escuchar en las direcciones URL configuradas con `IWebHostBuilder` en lugar de las que están configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="1d74e-733">**Clave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="1d74e-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="1d74e-734">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-735">**Predeterminado**: `true`</span><span class="sxs-lookup"><span data-stu-id="1d74e-735">**Default**: `true`</span></span>  
<span data-ttu-id="1d74e-736">**Variable de entorno**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="1d74e-737">Para establecer este valor, use la variable de entorno o llame a `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="1d74e-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="1d74e-738">PreventHostingStartup</span></span>

<span data-ttu-id="1d74e-739">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="1d74e-740">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="1d74e-741">**Clave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="1d74e-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="1d74e-742">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1d74e-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="1d74e-743">**Predeterminado**: `false`</span><span class="sxs-lookup"><span data-stu-id="1d74e-743">**Default**: `false`</span></span>  
<span data-ttu-id="1d74e-744">**Variable de entorno**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="1d74e-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="1d74e-745">Para establecer este valor, use la variable de entorno o llame a `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="1d74e-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="1d74e-746">StartupAssembly</span></span>

<span data-ttu-id="1d74e-747">Ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="1d74e-748">**Clave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="1d74e-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="1d74e-749">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-749">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-750">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1d74e-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="1d74e-751">**Variable de entorno**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="1d74e-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="1d74e-752">Para establecer este valor, use la variable de entorno o llame a `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="1d74e-753">`UseStartup` puede tomar un nombre del ensamblado (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="1d74e-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="1d74e-754">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="1d74e-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="1d74e-755">Direcciones URL</span><span class="sxs-lookup"><span data-stu-id="1d74e-755">URLs</span></span>

<span data-ttu-id="1d74e-756">Lista delimitada por punto y coma de las direcciones IP o las direcciones de host con los puertos y protocolos en los que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1d74e-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="1d74e-757">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="1d74e-758">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="1d74e-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="1d74e-759">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="1d74e-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="1d74e-760">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="1d74e-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="1d74e-761">**Clave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="1d74e-761">**Key**: `urls`</span></span>  
<span data-ttu-id="1d74e-762">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-762">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-763">**Predeterminado**: `http://localhost:5000` y `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="1d74e-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="1d74e-764">**Variable de entorno**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="1d74e-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="1d74e-765">Para establecer este valor, use la variable de entorno o llame a `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="1d74e-766">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="1d74e-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="1d74e-767">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="1d74e-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-768">WebRoot</span></span>

<span data-ttu-id="1d74e-769">La propiedad [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="1d74e-770">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="1d74e-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="1d74e-771">**Clave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="1d74e-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="1d74e-772">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="1d74e-772">**Type**: `string`</span></span>  
<span data-ttu-id="1d74e-773">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="1d74e-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="1d74e-774">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1d74e-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="1d74e-775">**Variable de entorno**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="1d74e-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="1d74e-776">Para establecer este valor, use la variable de entorno o llame a `UseWebRoot` en `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1d74e-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="1d74e-777">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1d74e-777">For more information, see:</span></span>

* [<span data-ttu-id="1d74e-778">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="1d74e-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="1d74e-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="1d74e-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="1d74e-780">Administración de la vigencia del host</span><span class="sxs-lookup"><span data-stu-id="1d74e-780">Manage the host lifetime</span></span>

<span data-ttu-id="1d74e-781">Llame a los métodos en la implementación de <xref:Microsoft.Extensions.Hosting.IHost> creada para iniciar y detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1d74e-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="1d74e-782">Estos métodos afectan a todas las implementaciones de <xref:Microsoft.Extensions.Hosting.IHostedService> registradas en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="1d74e-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="1d74e-783">Run</span><span class="sxs-lookup"><span data-stu-id="1d74e-783">Run</span></span>

<span data-ttu-id="1d74e-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> inicia la aplicación y bloquea el subproceso que realiza la llamada hasta que se cierre el host.</span><span class="sxs-lookup"><span data-stu-id="1d74e-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="1d74e-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-785">RunAsync</span></span>

<span data-ttu-id="1d74e-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> inicia la aplicación y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="1d74e-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="1d74e-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-787">RunConsoleAsync</span></span>

<span data-ttu-id="1d74e-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita la compatibilidad de la consola, compila e inicia el host, y espera a <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM para el apagado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="1d74e-789">Iniciar</span><span class="sxs-lookup"><span data-stu-id="1d74e-789">Start</span></span>

<span data-ttu-id="1d74e-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia el host de forma sincrónica.</span><span class="sxs-lookup"><span data-stu-id="1d74e-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="1d74e-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-791">StartAsync</span></span>

<span data-ttu-id="1d74e-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia el host y devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el token de cancelación o el cierre.</span><span class="sxs-lookup"><span data-stu-id="1d74e-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="1d74e-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se llama al inicio de `StartAsync`, que espera hasta que se complete antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="1d74e-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="1d74e-794">Esto se puede usar para retrasar el inicio hasta que lo indique un evento externo.</span><span class="sxs-lookup"><span data-stu-id="1d74e-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="1d74e-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-795">StopAsync</span></span>

<span data-ttu-id="1d74e-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> intenta detener el host en el tiempo de espera proporcionado.</span><span class="sxs-lookup"><span data-stu-id="1d74e-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="1d74e-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="1d74e-797">WaitForShutdown</span></span>

<span data-ttu-id="1d74e-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloquea el subproceso de llamada hasta que IHostLifetime desencadena el apagado, por ejemplo, a través de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="1d74e-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="1d74e-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="1d74e-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="1d74e-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> devuelve <xref:System.Threading.Tasks.Task>, que se completa cuando se desencadena el cierre a través del token determinado y llama a <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1d74e-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="1d74e-801">Control externo</span><span class="sxs-lookup"><span data-stu-id="1d74e-801">External control</span></span>

<span data-ttu-id="1d74e-802">El control directo de la vigencia del host se puede lograr mediante métodos a los que se puede llamar de forma externa:</span><span class="sxs-lookup"><span data-stu-id="1d74e-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="1d74e-803">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1d74e-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
