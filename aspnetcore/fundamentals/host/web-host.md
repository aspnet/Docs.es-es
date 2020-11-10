---
title: Host web de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el host web en ASP.NET Core, que es responsable de la administración de inicio y duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/host/web-host
ms.openlocfilehash: 09383cb9067d7fdc2d7b69213b741e7ae823e9ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060019"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="1b4d8-103">Host web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b4d8-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="1b4d8-104">Las aplicaciones de ASP.NET Core configuran e inician un *host*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="1b4d8-105">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="1b4d8-106">Como mínimo, el host configura un servidor y una canalización de procesamiento de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="1b4d8-107">El host también puede configurar el registro, la inserción de dependencias y la configuración.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b4d8-108">En el artículo se explica el host web, que solo permanece disponible para compatibilidad con versiones anteriores:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="1b4d8-109">Se recomienda el [host genérico](xref:fundamentals/host/generic-host) para todos los tipos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b4d8-110">En este artículo se describe el host web, que sirve para hospedar aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="1b4d8-111">Para otros tipos de aplicaciones, use el [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="1b4d8-112">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="1b4d8-112">Set up a host</span></span>

<span data-ttu-id="1b4d8-113">Cree un host con una instancia de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="1b4d8-114">Normalmente, esto se realiza en el punto de entrada de la aplicación, el método `Main`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="1b4d8-115">En las plantillas de proyecto, `Main` se encuentra en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="1b4d8-116">Una aplicación típica llama a [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para empezar a configurar un host:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="1b4d8-117">El código que llama a `CreateDefaultBuilder` está en un método denominado `CreateWebHostBuilder`, que lo diferencia del código de `Main` que llama a `Run` en el objeto generador.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="1b4d8-118">Dicha diferencia es necesaria si usa [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="1b4d8-119">Las herramientas esperan encontrar un método `CreateWebHostBuilder` al que pueden llamar en tiempo de diseño para configurar el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="1b4d8-120">Una alternativa consiste en implementar `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="1b4d8-121">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="1b4d8-122">`CreateDefaultBuilder` realiza las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="1b4d8-123">Configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="1b4d8-124">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="1b4d8-125">Establece la [raíz del contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="1b4d8-126">Carga la [configuración de host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="1b4d8-127">Variables de entorno con el prefijo `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="1b4d8-128">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-128">Command-line arguments.</span></span>
* <span data-ttu-id="1b4d8-129">Carga la configuración de la aplicación en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="1b4d8-130">*:::no-loc(appsettings.json):::*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-130">*:::no-loc(appsettings.json):::*.</span></span>
  * <span data-ttu-id="1b4d8-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="1b4d8-132">[Administrador de secretos](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="1b4d8-133">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-133">Environment variables.</span></span>
  * <span data-ttu-id="1b4d8-134">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-134">Command-line arguments.</span></span>
* <span data-ttu-id="1b4d8-135">Configura el [registro](xref:fundamentals/logging/index) para la salida de consola y de depuración.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="1b4d8-136">El registro incluye reglas de [filtrado del registro](xref:fundamentals/logging/index#log-filtering) especificadas en una sección de configuración de registro de un archivo *:::no-loc(appsettings.json):::* o *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="1b4d8-137">Cuando se ejecuta detrás de IIS con el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` habilita la [integración de IIS](xref:host-and-deploy/iis/index), que configura la dirección base y el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="1b4d8-138">La integración de IIS también configura la aplicación para que [capture errores de inicio](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="1b4d8-139">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="1b4d8-140">Establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="1b4d8-141">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="1b4d8-142">La configuración definida en `CreateDefaultBuilder` se puede reemplazar y aumentar mediante [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) y otros métodos y métodos de extensión de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="1b4d8-143">A continuación, se presentan algunos ejemplos:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-143">A few examples follow:</span></span>

* <span data-ttu-id="1b4d8-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se usa para especificar `IConfiguration` adicionales para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="1b4d8-145">La siguiente llamada `ConfigureAppConfiguration` agrega un delegado para incluir la configuración de la aplicación en el archivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="1b4d8-146">Es posible llamar a `ConfigureAppConfiguration` varias veces.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="1b4d8-147">Tenga en cuenta que esta configuración no se aplica al host (por ejemplo, direcciones URL de servidor o entorno).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="1b4d8-148">Vea la sección [Valores de configuración de host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="1b4d8-149">La siguiente llamada `ConfigureLogging` agrega un delegado para configurar el nivel de registro mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) en [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="1b4d8-150">Esta configuración reemplaza la configuración de *appsettings.Development.JSON* (`LogLevel.Debug`) y *appsettings.Production.JSON* (`LogLevel.Error`) configurada mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="1b4d8-151">Es posible llamar a `ConfigureLogging` varias veces.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="1b4d8-152">La siguiente llamada a `ConfigureKestrel` reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="1b4d8-153">La siguiente llamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="1b4d8-154">La [raíz del contenido](xref:fundamentals/index#content-root) determina la ubicación en la que el host busca archivos de contenido como, por ejemplo, archivos de vista MVC.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="1b4d8-155">Cuando la aplicación se inicia desde la carpeta raíz del proyecto, esta se utiliza como la raíz del contenido.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="1b4d8-156">Este es el valor predeterminado usado en [Visual Studio](https://visualstudio.microsoft.com) y las [nuevas plantillas dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="1b4d8-157">Para obtener más información sobre la configuración de la aplicación, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="1b4d8-158">Como alternativa al uso del método estático `CreateDefaultBuilder`, crear un host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) es un enfoque compatible con ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="1b4d8-159">Al configurar un host, se pueden proporcionar los métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) y [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="1b4d8-160">Si se especifica una clase `Startup`, debe definir un método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="1b4d8-161">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="1b4d8-162">Varias llamadas a `ConfigureServices` se anexan entre sí.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="1b4d8-163">Varias llamadas a `Configure` o `UseStartup` en el `WebHostBuilder` reemplazan la configuración anterior.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1b4d8-164">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="1b4d8-164">Host configuration values</span></span>

<span data-ttu-id="1b4d8-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) se basa en los siguientes métodos para establecer los valores de configuración del host:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="1b4d8-166">Configuración del generador de host, que incluye las variables de entorno con el formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="1b4d8-167">Por ejemplo: `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="1b4d8-168">Extensiones como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) y [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (vea la sección [Invalidación de la configuración](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="1b4d8-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) y la clave asociada.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="1b4d8-170">Al establecer un valor con `UseSetting`, el valor se establece como una cadena, independientemente del tipo.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="1b4d8-171">El host usa cualquier opción que establece un valor en último lugar.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="1b4d8-172">Para obtener más información, consulte [Invalidación de la configuración](#override-configuration) en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="1b4d8-173">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="1b4d8-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b4d8-174">La propiedad `IWebHostEnvironment.ApplicationName` se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="1b4d8-175">El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="1b4d8-176">Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="1b4d8-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b4d8-177">La propiedad [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="1b4d8-178">El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="1b4d8-179">Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="1b4d8-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="1b4d8-180">**Clave** : nombreDeAplicación</span><span class="sxs-lookup"><span data-stu-id="1b4d8-180">**Key** : applicationName</span></span>  
<span data-ttu-id="1b4d8-181">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-181">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-182">**Valor predeterminado** : nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-182">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="1b4d8-183">**Establecer mediante** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-183">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="1b4d8-184">**Variable de entorno** : `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-184">**Environment variable** : `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="1b4d8-185">Capturar errores de inicio</span><span class="sxs-lookup"><span data-stu-id="1b4d8-185">Capture Startup Errors</span></span>

<span data-ttu-id="1b4d8-186">Esta configuración controla la captura de errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="1b4d8-187">**Clave** : captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="1b4d8-187">**Key** : captureStartupErrors</span></span>  
<span data-ttu-id="1b4d8-188">**Tipo** : *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1b4d8-188">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1b4d8-189">**Predeterminado** : `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-189">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="1b4d8-190">**Establecer mediante** : `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-190">**Set using** : `CaptureStartupErrors`</span></span>  
<span data-ttu-id="1b4d8-191">**Variable de entorno** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-191">**Environment variable** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="1b4d8-192">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="1b4d8-193">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="1b4d8-194">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1b4d8-194">Content root</span></span>

<span data-ttu-id="1b4d8-195">Esta configuración determina la ubicación en la que ASP.NET Core comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="1b4d8-196">**Clave** : contentRoot</span><span class="sxs-lookup"><span data-stu-id="1b4d8-196">**Key** : contentRoot</span></span>  
<span data-ttu-id="1b4d8-197">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-197">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-198">**Predeterminado** : la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-198">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="1b4d8-199">**Establecer mediante** : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-199">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="1b4d8-200">**Variable de entorno** : `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-200">**Environment variable** : `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="1b4d8-201">La raíz del contenido también se usa como la ruta de acceso base para la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="1b4d8-202">Si no existe la ruta de acceso de la raíz web, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="1b4d8-203">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-203">For more information, see:</span></span>

* [<span data-ttu-id="1b4d8-204">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1b4d8-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="1b4d8-205">Raíz web</span><span class="sxs-lookup"><span data-stu-id="1b4d8-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="1b4d8-206">Errores detallados</span><span class="sxs-lookup"><span data-stu-id="1b4d8-206">Detailed Errors</span></span>

<span data-ttu-id="1b4d8-207">Determina si se deben capturar los errores detallados.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="1b4d8-208">**Clave** : detailedErrors</span><span class="sxs-lookup"><span data-stu-id="1b4d8-208">**Key** : detailedErrors</span></span>  
<span data-ttu-id="1b4d8-209">**Tipo** : *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1b4d8-209">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1b4d8-210">**Valor predeterminado** : false</span><span class="sxs-lookup"><span data-stu-id="1b4d8-210">**Default** : false</span></span>  
<span data-ttu-id="1b4d8-211">**Establecer mediante** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-211">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="1b4d8-212">**Variable de entorno** : `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-212">**Environment variable** : `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="1b4d8-213">Cuando se habilita (o cuando el <a href="#environment">entorno</a> está establecido en `Development`), la aplicación captura excepciones detalladas.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="1b4d8-214">Entorno</span><span class="sxs-lookup"><span data-stu-id="1b4d8-214">Environment</span></span>

<span data-ttu-id="1b4d8-215">Establece el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-215">Sets the app's environment.</span></span>

<span data-ttu-id="1b4d8-216">**Clave** : environment</span><span class="sxs-lookup"><span data-stu-id="1b4d8-216">**Key** : environment</span></span>  
<span data-ttu-id="1b4d8-217">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-217">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-218">**Predeterminado** : Producción</span><span class="sxs-lookup"><span data-stu-id="1b4d8-218">**Default** : Production</span></span>  
<span data-ttu-id="1b4d8-219">**Establecer mediante** : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-219">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="1b4d8-220">**Variable de entorno** : `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-220">**Environment variable** : `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="1b4d8-221">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-221">The environment can be set to any value.</span></span> <span data-ttu-id="1b4d8-222">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="1b4d8-223">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-223">Values aren't case sensitive.</span></span> <span data-ttu-id="1b4d8-224">De forma predeterminada, el *entorno* se lee desde la variable de entorno `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1b4d8-225">Cuando se usa [Visual Studio](https://visualstudio.microsoft.com), las variables de entorno se pueden establecer en el archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="1b4d8-226">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="1b4d8-227">Ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="1b4d8-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="1b4d8-228">Establece los ensamblados de inicio de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="1b4d8-229">**Clave** : hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="1b4d8-229">**Key** : hostingStartupAssemblies</span></span>  
<span data-ttu-id="1b4d8-230">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-230">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-231">**Predeterminado** : Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1b4d8-231">**Default** : Empty string</span></span>  
<span data-ttu-id="1b4d8-232">**Establecer mediante** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-232">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="1b4d8-233">**Variable de entorno** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-233">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="1b4d8-234">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="1b4d8-235">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="1b4d8-236">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="1b4d8-237">Puerto HTTPS</span><span class="sxs-lookup"><span data-stu-id="1b4d8-237">HTTPS Port</span></span>

<span data-ttu-id="1b4d8-238">Establezca puerto de redirección HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="1b4d8-239">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="1b4d8-240">**Clave** : https_port **Tipo** : *cadena*
**Valor predeterminado** : no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-240">**Key** : https_port **Type** : *string*
**Default** : A default value isn't set.</span></span>
<span data-ttu-id="1b4d8-241">**Establecer mediante** : `UseSetting`
**Variable de entorno** : `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-241">**Set using** : `UseSetting`
**Environment variable** : `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="1b4d8-242">Ensamblados de exclusión de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="1b4d8-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="1b4d8-243">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="1b4d8-244">**Clave** : hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="1b4d8-244">**Key** : hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="1b4d8-245">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-245">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-246">**Predeterminado** : Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="1b4d8-246">**Default** : Empty string</span></span>  
<span data-ttu-id="1b4d8-247">**Establecer mediante** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-247">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="1b4d8-248">**Variable de entorno** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-248">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="1b4d8-249">Preferir las direcciones URL de hospedaje</span><span class="sxs-lookup"><span data-stu-id="1b4d8-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="1b4d8-250">Indica si el host debe escuchar en las direcciones URL configuradas con `WebHostBuilder` en lugar de las que estén configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="1b4d8-251">**Clave** : preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="1b4d8-251">**Key** : preferHostingUrls</span></span>  
<span data-ttu-id="1b4d8-252">**Tipo** : *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1b4d8-252">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1b4d8-253">**Valor predeterminado** : true</span><span class="sxs-lookup"><span data-stu-id="1b4d8-253">**Default** : true</span></span>  
<span data-ttu-id="1b4d8-254">**Establecer mediante** : `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-254">**Set using** : `PreferHostingUrls`</span></span>  
<span data-ttu-id="1b4d8-255">**Variable de entorno** : `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-255">**Environment variable** : `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="1b4d8-256">Evitar el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="1b4d8-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="1b4d8-257">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="1b4d8-258">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="1b4d8-259">**Clave** : preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="1b4d8-259">**Key** : preventHostingStartup</span></span>  
<span data-ttu-id="1b4d8-260">**Tipo** : *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="1b4d8-260">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1b4d8-261">**Valor predeterminado** : false</span><span class="sxs-lookup"><span data-stu-id="1b4d8-261">**Default** : false</span></span>  
<span data-ttu-id="1b4d8-262">**Establecer mediante** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-262">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="1b4d8-263">**Variable de entorno** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-263">**Environment variable** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="1b4d8-264">Direcciones URL de servidor</span><span class="sxs-lookup"><span data-stu-id="1b4d8-264">Server URLs</span></span>

<span data-ttu-id="1b4d8-265">Indica las direcciones IP o las direcciones de host con los puertos y protocolos en que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="1b4d8-266">**Clave** : urls</span><span class="sxs-lookup"><span data-stu-id="1b4d8-266">**Key** : urls</span></span>  
<span data-ttu-id="1b4d8-267">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-267">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-268">**Predeterminado** : http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="1b4d8-268">**Default** : http://localhost:5000</span></span>  
<span data-ttu-id="1b4d8-269">**Establecer mediante** : `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-269">**Set using** : `UseUrls`</span></span>  
<span data-ttu-id="1b4d8-270">**Variable de entorno** : `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-270">**Environment variable** : `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="1b4d8-271">Se establece una lista de prefijos de URL separados por punto y coma (;) a los que debe responder el servidor.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="1b4d8-272">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="1b4d8-273">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="1b4d8-274">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="1b4d8-275">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="1b4d8-276">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="1b4d8-277">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="1b4d8-278">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="1b4d8-278">Shutdown Timeout</span></span>

<span data-ttu-id="1b4d8-279">Especifica la cantidad de tiempo que se espera hasta el cierre del host web.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="1b4d8-280">**Clave** : shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="1b4d8-280">**Key** : shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="1b4d8-281">**Tipo** : *int*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-281">**Type** : *int*</span></span>  
<span data-ttu-id="1b4d8-282">**Predeterminado** : 5</span><span class="sxs-lookup"><span data-stu-id="1b4d8-282">**Default** : 5</span></span>  
<span data-ttu-id="1b4d8-283">**Establecer mediante** : `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-283">**Set using** : `UseShutdownTimeout`</span></span>  
<span data-ttu-id="1b4d8-284">**Variable de entorno** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-284">**Environment variable** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="1b4d8-285">Aunque la clave acepta un *int* con `UseSetting` (por ejemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), el método de extensión [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) toma [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="1b4d8-286">Durante el período de tiempo de espera, el hospedaje:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="1b4d8-287">Activa [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="1b4d8-288">Trata de detener los servicios hospedados y registra cualquier error que se produzca en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="1b4d8-289">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="1b4d8-290">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="1b4d8-291">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="1b4d8-292">Ensamblado de inicio</span><span class="sxs-lookup"><span data-stu-id="1b4d8-292">Startup Assembly</span></span>

<span data-ttu-id="1b4d8-293">Determina el ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="1b4d8-294">**Clave** : startupAssembly</span><span class="sxs-lookup"><span data-stu-id="1b4d8-294">**Key** : startupAssembly</span></span>  
<span data-ttu-id="1b4d8-295">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-295">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-296">**Predeterminado** : el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1b4d8-296">**Default** : The app's assembly</span></span>  
<span data-ttu-id="1b4d8-297">**Establecer mediante** : `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-297">**Set using** : `UseStartup`</span></span>  
<span data-ttu-id="1b4d8-298">**Variable de entorno** : `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-298">**Environment variable** : `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="1b4d8-299">Se puede hacer referencia al ensamblado por su nombre (`string`) o su tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="1b4d8-300">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="1b4d8-301">Raíz web</span><span class="sxs-lookup"><span data-stu-id="1b4d8-301">Web root</span></span>

<span data-ttu-id="1b4d8-302">Establece la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="1b4d8-303">**Clave** : webroot</span><span class="sxs-lookup"><span data-stu-id="1b4d8-303">**Key** : webroot</span></span>  
<span data-ttu-id="1b4d8-304">**Tipo** : *cadena*</span><span class="sxs-lookup"><span data-stu-id="1b4d8-304">**Type** : *string*</span></span>  
<span data-ttu-id="1b4d8-305">**Predeterminado** : De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-305">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="1b4d8-306">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="1b4d8-307">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="1b4d8-308">**Establecer mediante** : `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-308">**Set using** : `UseWebRoot`</span></span>  
<span data-ttu-id="1b4d8-309">**Variable de entorno** : `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="1b4d8-309">**Environment variable** : `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="1b4d8-310">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-310">For more information, see:</span></span>

* [<span data-ttu-id="1b4d8-311">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="1b4d8-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="1b4d8-312">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="1b4d8-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="1b4d8-313">Invalidación de la configuración</span><span class="sxs-lookup"><span data-stu-id="1b4d8-313">Override configuration</span></span>

<span data-ttu-id="1b4d8-314">Use [Configuración](xref:fundamentals/configuration/index) para configurar el host web.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="1b4d8-315">En el ejemplo siguiente, la configuración del host se especifica de forma opcional en un archivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="1b4d8-316">Cualquier configuración que se cargue del archivo *hostsettings.json* se puede reemplazar por argumentos de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="1b4d8-317">La configuración generada (en `config`) se usa para configurar el host con [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="1b4d8-318">La configuración de `IWebHostBuilder` se agrega a la de la aplicación, pero lo contrario no es aplicable: `ConfigureAppConfiguration` no afecta a la configuración de `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="1b4d8-319">Reemplazar primero la configuración proporcionada por `UseUrls` por la de *hostsettings.json* y, luego, por la del argumento de línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="1b4d8-320">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="1b4d8-320">*hostsettings.json* :</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="1b4d8-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) solo copia las claves del elemento `IConfiguration` proporcionado a la configuración del generador de host.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="1b4d8-322">Por consiguiente, el hecho de configurar `reloadOnChange: true` para los archivos de configuración XML, JSON e INI no tiene ningún efecto.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="1b4d8-323">Para especificar el host que se ejecuta en una dirección URL determinada, se puede pasar el valor deseado desde un símbolo del sistema al ejecutar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="1b4d8-324">El argumento de línea de comandos reemplaza el valor `urls` del archivo *hostsettings.json* , y el servidor efectúa la escucha en el puerto 8080:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="1b4d8-325">Administración del host</span><span class="sxs-lookup"><span data-stu-id="1b4d8-325">Manage the host</span></span>

<span data-ttu-id="1b4d8-326">**Run**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-326">**Run**</span></span>

<span data-ttu-id="1b4d8-327">El método `Run` inicia la aplicación web y bloquea el subproceso que realiza la llamada hasta que se apague el host:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="1b4d8-328">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-328">**Start**</span></span>

<span data-ttu-id="1b4d8-329">Ejecute el host de manera que se evite un bloqueo mediante una llamada a su método `Start`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="1b4d8-330">Si se pasa una lista de direcciones URL al método `Start`, la escucha se produce en las direcciones URL especificadas:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="1b4d8-331">La aplicación puede inicializar un nuevo host usando los valores preconfigurados de `CreateDefaultBuilder` mediante un método práctico estático.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="1b4d8-332">Estos métodos inician el servidor sin la salida de la consola y con [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) esperando una interrupción (Ctrl-C/SIGINT o SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="1b4d8-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="1b4d8-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="1b4d8-334">Start con `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-335">Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="1b4d8-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="1b4d8-336">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1b4d8-337">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1b4d8-338">**Start(url de cadena, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="1b4d8-339">Start con una dirección URL y `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-340">Produce el mismo resultado que **Start(RequestDelegate app)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-340">Produces the same result as **Start(RequestDelegate app)** , except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="1b4d8-341">**Start(acción\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="1b4d8-342">Use una instancia de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar el middleware de enrutamiento:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-343">Utilice las siguientes solicitudes de explorador con el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="1b4d8-344">Request</span><span class="sxs-lookup"><span data-stu-id="1b4d8-344">Request</span></span>                                    | <span data-ttu-id="1b4d8-345">Respuesta</span><span class="sxs-lookup"><span data-stu-id="1b4d8-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="1b4d8-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="1b4d8-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="1b4d8-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="1b4d8-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="1b4d8-348">Produce una excepción con la cadena "ooops!"</span><span class="sxs-lookup"><span data-stu-id="1b4d8-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="1b4d8-349">Produce una excepción con la cadena "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="1b4d8-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="1b4d8-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="1b4d8-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="1b4d8-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="1b4d8-351">Hello World!</span></span>                             |

<span data-ttu-id="1b4d8-352">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1b4d8-353">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1b4d8-354">**Start(url de cadena, acción\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="1b4d8-355">Use una dirección URL y una instancia de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-356">Produce el mismo resultado que **Start(acción\<IRouteBuilder> routeBuilder)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)** , except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="1b4d8-357">**StartWith(acción\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="1b4d8-358">Proporciona un delegado para configurar `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-359">Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="1b4d8-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="1b4d8-360">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1b4d8-361">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1b4d8-362">**StartWith(url de cadena, acción\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="1b4d8-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="1b4d8-363">Proporcione una dirección URL y un delegado para configurar `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1b4d8-364">Produce el mismo resultado que **StartWith(acción\<IApplicationBuilder> app)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)** , except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="1b4d8-365">Interfaz IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="1b4d8-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="1b4d8-366">La interfaz `IWebHostEnvironment` proporciona información sobre el entorno de hospedaje web de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="1b4d8-367">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IWebHostEnvironment` a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="1b4d8-368">Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="1b4d8-369">Como alternativa, inserte `IWebHostEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="1b4d8-370">Además del método de extensión `IsDevelopment`, `IWebHostEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="1b4d8-371">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="1b4d8-372">El servicio `IWebHostEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="1b4d8-373">`IWebHostEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="1b4d8-374">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="1b4d8-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="1b4d8-375">La [interfaz IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) proporciona información sobre el entorno de hospedaje web de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="1b4d8-376">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IHostingEnvironment` a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="1b4d8-377">Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="1b4d8-378">Como alternativa, inserte `IHostingEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="1b4d8-379">Además del método de extensión `IsDevelopment`, `IHostingEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="1b4d8-380">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="1b4d8-381">El servicio `IHostingEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="1b4d8-382">`IHostingEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="1b4d8-383">Interfaz IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1b4d8-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="1b4d8-384">`IHostApplicationLifetime` permite actividades posteriores al inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="1b4d8-385">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="1b4d8-386">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="1b4d8-386">Cancellation Token</span></span>    | <span data-ttu-id="1b4d8-387">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="1b4d8-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="1b4d8-388">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="1b4d8-389">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="1b4d8-390">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-390">All requests should be processed.</span></span> <span data-ttu-id="1b4d8-391">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="1b4d8-392">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="1b4d8-393">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-393">Requests may still be processing.</span></span> <span data-ttu-id="1b4d8-394">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="1b4d8-395">`StopApplication` solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="1b4d8-396">La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="1b4d8-397">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="1b4d8-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="1b4d8-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite actividades posteriores al inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="1b4d8-399">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="1b4d8-400">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="1b4d8-400">Cancellation Token</span></span>    | <span data-ttu-id="1b4d8-401">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="1b4d8-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="1b4d8-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="1b4d8-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="1b4d8-403">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-403">The host has fully started.</span></span> |
| [<span data-ttu-id="1b4d8-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="1b4d8-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="1b4d8-405">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="1b4d8-406">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-406">All requests should be processed.</span></span> <span data-ttu-id="1b4d8-407">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="1b4d8-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="1b4d8-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="1b4d8-409">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="1b4d8-410">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-410">Requests may still be processing.</span></span> <span data-ttu-id="1b4d8-411">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="1b4d8-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="1b4d8-413">La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="1b4d8-414">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="1b4d8-414">Scope validation</span></span>

<span data-ttu-id="1b4d8-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="1b4d8-416">Cuando `ValidateScopes` está establecido en `true`, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="1b4d8-417">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="1b4d8-418">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="1b4d8-419">El proveedor de servicios raíz se crea cuando se llama a [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="1b4d8-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="1b4d8-420">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="1b4d8-421">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="1b4d8-422">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="1b4d8-423">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="1b4d8-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="1b4d8-424">Para validar los ámbitos siempre, incluso en el entorno de producción, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) con [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) en el generador de hosts:</span><span class="sxs-lookup"><span data-stu-id="1b4d8-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="1b4d8-425">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1b4d8-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
