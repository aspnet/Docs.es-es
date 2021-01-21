---
title: Host web de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre el host web en ASP.NET Core, que es responsable de la administración de inicio y duración de la aplicación.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 98be96bf60441cf09a315dbd1c60e109a7a08afe
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253116"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="612a0-103">Host web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="612a0-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="612a0-104">Las aplicaciones de ASP.NET Core configuran e inician un *host*.</span><span class="sxs-lookup"><span data-stu-id="612a0-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="612a0-105">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="612a0-106">Como mínimo, el host configura un servidor y una canalización de procesamiento de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="612a0-107">El host también puede configurar el registro, la inserción de dependencias y la configuración.</span><span class="sxs-lookup"><span data-stu-id="612a0-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="612a0-108">En el artículo se explica el host web, que solo permanece disponible para compatibilidad con versiones anteriores:</span><span class="sxs-lookup"><span data-stu-id="612a0-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="612a0-109">Se recomienda el [host genérico](xref:fundamentals/host/generic-host) para todos los tipos de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="612a0-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="612a0-110">En este artículo se describe el host web, que sirve para hospedar aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="612a0-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="612a0-111">Para otros tipos de aplicaciones, use el [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="612a0-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="612a0-112">Configuración de un host</span><span class="sxs-lookup"><span data-stu-id="612a0-112">Set up a host</span></span>

<span data-ttu-id="612a0-113">Cree un host con una instancia de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="612a0-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="612a0-114">Normalmente, esto se realiza en el punto de entrada de la aplicación, el método `Main`.</span><span class="sxs-lookup"><span data-stu-id="612a0-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="612a0-115">En las plantillas de proyecto, `Main` se encuentra en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="612a0-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="612a0-116">Una aplicación típica llama a [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para empezar a configurar un host:</span><span class="sxs-lookup"><span data-stu-id="612a0-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="612a0-117">El código que llama a `CreateDefaultBuilder` está en un método denominado `CreateWebHostBuilder`, que lo diferencia del código de `Main` que llama a `Run` en el objeto generador.</span><span class="sxs-lookup"><span data-stu-id="612a0-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="612a0-118">Dicha diferencia es necesaria si usa [herramientas de Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="612a0-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="612a0-119">Las herramientas esperan encontrar un método `CreateWebHostBuilder` al que pueden llamar en tiempo de diseño para configurar el host sin ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="612a0-120">Una alternativa consiste en implementar `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="612a0-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="612a0-121">Para obtener más información, consulte [Creación de DbContext en tiempo de diseño](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="612a0-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="612a0-122">`CreateDefaultBuilder` realiza las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="612a0-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="612a0-123">Configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="612a0-124">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="612a0-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="612a0-125">Configura el servidor [Kestrel](xref:fundamentals/servers/kestrel) como servidor web por medio de los proveedores de configuración de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="612a0-126">Para conocer las opciones predeterminadas del servidor Kestrel, consulte <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="612a0-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="612a0-127">Establece la [raíz del contenido](xref:fundamentals/index#content-root) en la ruta de acceso devuelta por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="612a0-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="612a0-128">Carga la [configuración de host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="612a0-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="612a0-129">Variables de entorno con el prefijo `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="612a0-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="612a0-130">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="612a0-130">Command-line arguments.</span></span>
* <span data-ttu-id="612a0-131">Carga la configuración de la aplicación en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="612a0-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="612a0-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="612a0-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="612a0-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="612a0-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="612a0-134">[Secretos del usuario](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="612a0-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="612a0-135">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="612a0-135">Environment variables.</span></span>
  * <span data-ttu-id="612a0-136">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="612a0-136">Command-line arguments.</span></span>
* <span data-ttu-id="612a0-137">Configura el [registro](xref:fundamentals/logging/index) para la salida de consola y de depuración.</span><span class="sxs-lookup"><span data-stu-id="612a0-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="612a0-138">El registro incluye reglas de [filtrado del registro](xref:fundamentals/logging/index#log-filtering) especificadas en una sección de configuración de registro de un archivo *appsettings.json* o *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="612a0-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="612a0-139">Cuando se ejecuta detrás de IIS con el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` habilita la [integración de IIS](xref:host-and-deploy/iis/index), que configura la dirección base y el puerto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="612a0-140">La integración de IIS también configura la aplicación para que [capture errores de inicio](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="612a0-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="612a0-141">Para consultar las opciones predeterminadas de IIS, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="612a0-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="612a0-142">Establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo.</span><span class="sxs-lookup"><span data-stu-id="612a0-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="612a0-143">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="612a0-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="612a0-144">La configuración definida en `CreateDefaultBuilder` se puede reemplazar y aumentar mediante [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) y otros métodos y métodos de extensión de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="612a0-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="612a0-145">A continuación, se presentan algunos ejemplos:</span><span class="sxs-lookup"><span data-stu-id="612a0-145">A few examples follow:</span></span>

* <span data-ttu-id="612a0-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se usa para especificar `IConfiguration` adicionales para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="612a0-147">La siguiente llamada `ConfigureAppConfiguration` agrega un delegado para incluir la configuración de la aplicación en el archivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="612a0-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="612a0-148">Es posible llamar a `ConfigureAppConfiguration` varias veces.</span><span class="sxs-lookup"><span data-stu-id="612a0-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="612a0-149">Tenga en cuenta que esta configuración no se aplica al host (por ejemplo, direcciones URL de servidor o entorno).</span><span class="sxs-lookup"><span data-stu-id="612a0-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="612a0-150">Vea la sección [Valores de configuración de host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="612a0-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="612a0-151">La siguiente llamada `ConfigureLogging` agrega un delegado para configurar el nivel de registro mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) en [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="612a0-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="612a0-152">Esta configuración reemplaza la configuración de *appsettings.Development.JSON* (`LogLevel.Debug`) y *appsettings.Production.JSON* (`LogLevel.Error`) configurada mediante `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="612a0-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="612a0-153">Es posible llamar a `ConfigureLogging` varias veces.</span><span class="sxs-lookup"><span data-stu-id="612a0-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="612a0-154">La siguiente llamada a `ConfigureKestrel` reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="612a0-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="612a0-155">La siguiente llamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) reemplaza el valor predeterminado de [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 000 000 bytes establecido al configurar Kestrel mediante `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="612a0-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="612a0-156">La [raíz del contenido](xref:fundamentals/index#content-root) determina la ubicación en la que el host busca archivos de contenido como, por ejemplo, archivos de vista MVC.</span><span class="sxs-lookup"><span data-stu-id="612a0-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="612a0-157">Cuando la aplicación se inicia desde la carpeta raíz del proyecto, esta se utiliza como la raíz del contenido.</span><span class="sxs-lookup"><span data-stu-id="612a0-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="612a0-158">Este es el valor predeterminado usado en [Visual Studio](https://visualstudio.microsoft.com) y las [nuevas plantillas dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="612a0-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="612a0-159">Para obtener más información sobre la configuración de la aplicación, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="612a0-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="612a0-160">Como alternativa al uso del método estático `CreateDefaultBuilder`, crear un host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) es un enfoque compatible con ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="612a0-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="612a0-161">Al configurar un host, se pueden proporcionar los métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) y [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices).</span><span class="sxs-lookup"><span data-stu-id="612a0-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="612a0-162">Si se especifica una clase `Startup`, debe definir un método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="612a0-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="612a0-163">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="612a0-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="612a0-164">Varias llamadas a `ConfigureServices` se anexan entre sí.</span><span class="sxs-lookup"><span data-stu-id="612a0-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="612a0-165">Varias llamadas a `Configure` o `UseStartup` en el `WebHostBuilder` reemplazan la configuración anterior.</span><span class="sxs-lookup"><span data-stu-id="612a0-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="612a0-166">Valores de configuración de host</span><span class="sxs-lookup"><span data-stu-id="612a0-166">Host configuration values</span></span>

<span data-ttu-id="612a0-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) se basa en los siguientes métodos para establecer los valores de configuración del host:</span><span class="sxs-lookup"><span data-stu-id="612a0-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="612a0-168">Configuración del generador de host, que incluye las variables de entorno con el formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="612a0-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="612a0-169">Por ejemplo: `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="612a0-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="612a0-170">Extensiones como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) y [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (vea la sección [Invalidación de la configuración](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="612a0-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="612a0-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) y la clave asociada.</span><span class="sxs-lookup"><span data-stu-id="612a0-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="612a0-172">Al establecer un valor con `UseSetting`, el valor se establece como una cadena, independientemente del tipo.</span><span class="sxs-lookup"><span data-stu-id="612a0-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="612a0-173">El host usa cualquier opción que establece un valor en último lugar.</span><span class="sxs-lookup"><span data-stu-id="612a0-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="612a0-174">Para obtener más información, consulte [Invalidación de la configuración](#override-configuration) en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="612a0-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="612a0-175">Clave de aplicación (nombre)</span><span class="sxs-lookup"><span data-stu-id="612a0-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="612a0-176">La propiedad `IWebHostEnvironment.ApplicationName` se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="612a0-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="612a0-177">El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="612a0-178">Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="612a0-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="612a0-179">La propiedad [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) se establece automáticamente cuando se llama a [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) o [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) durante la construcción del host.</span><span class="sxs-lookup"><span data-stu-id="612a0-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="612a0-180">El valor se establece en el nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="612a0-181">Para establecer el valor explícitamente, use [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="612a0-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="612a0-182">**Clave**: nombreDeAplicación</span><span class="sxs-lookup"><span data-stu-id="612a0-182">**Key**: applicationName</span></span>  
<span data-ttu-id="612a0-183">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-183">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-184">**Valor predeterminado**: nombre del ensamblado que contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="612a0-185">**Establecer mediante**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="612a0-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="612a0-186">**Variable de entorno**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="612a0-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="612a0-187">Capturar errores de inicio</span><span class="sxs-lookup"><span data-stu-id="612a0-187">Capture Startup Errors</span></span>

<span data-ttu-id="612a0-188">Esta configuración controla la captura de errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="612a0-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="612a0-189">**Clave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="612a0-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="612a0-190">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="612a0-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="612a0-191">**Predeterminado**: `false`, a menos que la aplicación se ejecute con Kestrel detrás de IIS, en cuyo caso el valor predeterminado es `true`.</span><span class="sxs-lookup"><span data-stu-id="612a0-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="612a0-192">**Establecer mediante**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="612a0-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="612a0-193">**Variable de entorno**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="612a0-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="612a0-194">Cuando es `false`, los errores durante el inicio provocan la salida del host.</span><span class="sxs-lookup"><span data-stu-id="612a0-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="612a0-195">Cuando es `true`, el host captura las excepciones producidas durante el inicio e intenta iniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="612a0-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="612a0-196">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="612a0-196">Content root</span></span>

<span data-ttu-id="612a0-197">Esta configuración determina la ubicación en la que ASP.NET Core comienza a buscar archivos de contenido.</span><span class="sxs-lookup"><span data-stu-id="612a0-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="612a0-198">**Clave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="612a0-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="612a0-199">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-199">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-200">**Predeterminado**: la carpeta donde se encuentra el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="612a0-201">**Establecer mediante**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="612a0-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="612a0-202">**Variable de entorno**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="612a0-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="612a0-203">La raíz del contenido también se usa como la ruta de acceso base para la [raíz web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="612a0-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="612a0-204">Si no existe la ruta de acceso de la raíz web, el host no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="612a0-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="612a0-205">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="612a0-205">For more information, see:</span></span>

* [<span data-ttu-id="612a0-206">Aspectos básicos: Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="612a0-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="612a0-207">Raíz web</span><span class="sxs-lookup"><span data-stu-id="612a0-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="612a0-208">Errores detallados</span><span class="sxs-lookup"><span data-stu-id="612a0-208">Detailed Errors</span></span>

<span data-ttu-id="612a0-209">Determina si se deben capturar los errores detallados.</span><span class="sxs-lookup"><span data-stu-id="612a0-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="612a0-210">**Clave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="612a0-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="612a0-211">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="612a0-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="612a0-212">**Valor predeterminado**: false</span><span class="sxs-lookup"><span data-stu-id="612a0-212">**Default**: false</span></span>  
<span data-ttu-id="612a0-213">**Establecer mediante**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="612a0-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="612a0-214">**Variable de entorno**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="612a0-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="612a0-215">Cuando se habilita (o cuando el <a href="#environment">entorno</a> está establecido en `Development`), la aplicación captura excepciones detalladas.</span><span class="sxs-lookup"><span data-stu-id="612a0-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="612a0-216">Entorno</span><span class="sxs-lookup"><span data-stu-id="612a0-216">Environment</span></span>

<span data-ttu-id="612a0-217">Establece el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-217">Sets the app's environment.</span></span>

<span data-ttu-id="612a0-218">**Clave**: environment</span><span class="sxs-lookup"><span data-stu-id="612a0-218">**Key**: environment</span></span>  
<span data-ttu-id="612a0-219">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-219">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-220">**Predeterminado**: Producción</span><span class="sxs-lookup"><span data-stu-id="612a0-220">**Default**: Production</span></span>  
<span data-ttu-id="612a0-221">**Establecer mediante**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="612a0-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="612a0-222">**Variable de entorno**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="612a0-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="612a0-223">El entorno se puede establecer en cualquier valor.</span><span class="sxs-lookup"><span data-stu-id="612a0-223">The environment can be set to any value.</span></span> <span data-ttu-id="612a0-224">Los valores definidos por el marco son `Development`, `Staging` y `Production`.</span><span class="sxs-lookup"><span data-stu-id="612a0-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="612a0-225">Los valores no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="612a0-225">Values aren't case sensitive.</span></span> <span data-ttu-id="612a0-226">De forma predeterminada, el *entorno* se lee desde la variable de entorno `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="612a0-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="612a0-227">Cuando se usa [Visual Studio](https://visualstudio.microsoft.com), las variables de entorno se pueden establecer en el archivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="612a0-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="612a0-228">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="612a0-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="612a0-229">Ensamblados de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="612a0-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="612a0-230">Establece los ensamblados de inicio de hospedaje de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="612a0-231">**Clave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="612a0-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="612a0-232">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-232">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-233">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="612a0-233">**Default**: Empty string</span></span>  
<span data-ttu-id="612a0-234">**Establecer mediante**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="612a0-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="612a0-235">**Variable de entorno**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="612a0-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="612a0-236">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para cargar en el inicio.</span><span class="sxs-lookup"><span data-stu-id="612a0-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="612a0-237">Aunque el valor de configuración predeterminado es una cadena vacía, los ensamblados de inicio de hospedaje incluyen siempre el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="612a0-238">Cuando se especifican los ensamblados de inicio de hospedaje, estos se agregan al ensamblado de la aplicación para que se carguen cuando la aplicación genera sus servicios comunes durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="612a0-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="612a0-239">Puerto HTTPS</span><span class="sxs-lookup"><span data-stu-id="612a0-239">HTTPS Port</span></span>

<span data-ttu-id="612a0-240">Establezca puerto de redirección HTTPS.</span><span class="sxs-lookup"><span data-stu-id="612a0-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="612a0-241">Se usa en [Exigir HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="612a0-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="612a0-242">**Clave**: https_port **Tipo**: *cadena*
**Valor predeterminado**: no se ha establecido ningún valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="612a0-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="612a0-243">**Establecer mediante**: `UseSetting`
**Variable de entorno**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="612a0-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="612a0-244">Ensamblados de exclusión de inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="612a0-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="612a0-245">Una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir en el inicio.</span><span class="sxs-lookup"><span data-stu-id="612a0-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="612a0-246">**Clave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="612a0-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="612a0-247">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-247">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-248">**Predeterminado**: Cadena vacía</span><span class="sxs-lookup"><span data-stu-id="612a0-248">**Default**: Empty string</span></span>  
<span data-ttu-id="612a0-249">**Establecer mediante**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="612a0-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="612a0-250">**Variable de entorno**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="612a0-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="612a0-251">Preferir las direcciones URL de hospedaje</span><span class="sxs-lookup"><span data-stu-id="612a0-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="612a0-252">Indica si el host debe escuchar en las direcciones URL configuradas con `WebHostBuilder` en lugar de las que estén configuradas con la implementación de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="612a0-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="612a0-253">**Clave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="612a0-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="612a0-254">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="612a0-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="612a0-255">**Valor predeterminado**: true</span><span class="sxs-lookup"><span data-stu-id="612a0-255">**Default**: true</span></span>  
<span data-ttu-id="612a0-256">**Establecer mediante**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="612a0-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="612a0-257">**Variable de entorno**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="612a0-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="612a0-258">Evitar el inicio de hospedaje</span><span class="sxs-lookup"><span data-stu-id="612a0-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="612a0-259">Impide la carga automática de los ensamblados de inicio de hospedaje, incluidos los configurados por el ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="612a0-260">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="612a0-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="612a0-261">**Clave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="612a0-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="612a0-262">**Tipo**: *bool* (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="612a0-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="612a0-263">**Valor predeterminado**: false</span><span class="sxs-lookup"><span data-stu-id="612a0-263">**Default**: false</span></span>  
<span data-ttu-id="612a0-264">**Establecer mediante**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="612a0-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="612a0-265">**Variable de entorno**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="612a0-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="612a0-266">Direcciones URL de servidor</span><span class="sxs-lookup"><span data-stu-id="612a0-266">Server URLs</span></span>

<span data-ttu-id="612a0-267">Indica las direcciones IP o las direcciones de host con los puertos y protocolos en que el servidor debe escuchar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="612a0-268">**Clave**: urls</span><span class="sxs-lookup"><span data-stu-id="612a0-268">**Key**: urls</span></span>  
<span data-ttu-id="612a0-269">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-269">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-270">**Predeterminado**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="612a0-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="612a0-271">**Establecer mediante**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="612a0-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="612a0-272">**Variable de entorno**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="612a0-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="612a0-273">Se establece una lista de prefijos de URL separados por punto y coma (;) a los que debe responder el servidor.</span><span class="sxs-lookup"><span data-stu-id="612a0-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="612a0-274">Por ejemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="612a0-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="612a0-275">Use "\*" para indicar que el servidor debe escuchar las solicitudes en cualquier dirección IP o nombre de host con el puerto y el protocolo especificados (por ejemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="612a0-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="612a0-276">El protocolo (`http://` o `https://`) debe incluirse con cada dirección URL.</span><span class="sxs-lookup"><span data-stu-id="612a0-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="612a0-277">Los formatos admitidos varían de un servidor a otro.</span><span class="sxs-lookup"><span data-stu-id="612a0-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="612a0-278">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="612a0-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="612a0-279">Para obtener más información, vea <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="612a0-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="612a0-280">Kestrel tiene su propia API de configuración de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="612a0-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="612a0-281">Para obtener más información, vea <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="612a0-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="612a0-282">Tiempo de espera de apagado</span><span class="sxs-lookup"><span data-stu-id="612a0-282">Shutdown Timeout</span></span>

<span data-ttu-id="612a0-283">Especifica la cantidad de tiempo que se espera hasta el cierre del host web.</span><span class="sxs-lookup"><span data-stu-id="612a0-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="612a0-284">**Clave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="612a0-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="612a0-285">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="612a0-285">**Type**: *int*</span></span>  
<span data-ttu-id="612a0-286">**Predeterminado**: 5</span><span class="sxs-lookup"><span data-stu-id="612a0-286">**Default**: 5</span></span>  
<span data-ttu-id="612a0-287">**Establecer mediante**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="612a0-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="612a0-288">**Variable de entorno**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="612a0-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="612a0-289">Aunque la clave acepta un *int* con `UseSetting` (por ejemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), el método de extensión [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) toma [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="612a0-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="612a0-290">Durante el período de tiempo de espera, el hospedaje:</span><span class="sxs-lookup"><span data-stu-id="612a0-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="612a0-291">Activa [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="612a0-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="612a0-292">Trata de detener los servicios hospedados y registra cualquier error que se produzca en los servicios que no se puedan detener.</span><span class="sxs-lookup"><span data-stu-id="612a0-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="612a0-293">Si el período de tiempo de espera expira antes de que todos los servicios hospedados se hayan detenido, los servicios activos que queden se detendrán cuando se cierre la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="612a0-294">Los servicios se detienen aun cuando no hayan terminado de procesarse.</span><span class="sxs-lookup"><span data-stu-id="612a0-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="612a0-295">Si los servicios requieren más tiempo para detenerse, aumente el valor de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="612a0-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="612a0-296">Ensamblado de inicio</span><span class="sxs-lookup"><span data-stu-id="612a0-296">Startup Assembly</span></span>

<span data-ttu-id="612a0-297">Determina el ensamblado en el que se va a buscar la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="612a0-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="612a0-298">**Clave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="612a0-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="612a0-299">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-299">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-300">**Predeterminado**: el ensamblado de la aplicación</span><span class="sxs-lookup"><span data-stu-id="612a0-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="612a0-301">**Establecer mediante**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="612a0-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="612a0-302">**Variable de entorno**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="612a0-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="612a0-303">Se puede hacer referencia al ensamblado por su nombre (`string`) o su tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="612a0-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="612a0-304">Si se llama a varios métodos `UseStartup`, la última llamada tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="612a0-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="612a0-305">Raíz web</span><span class="sxs-lookup"><span data-stu-id="612a0-305">Web root</span></span>

<span data-ttu-id="612a0-306">Establece la ruta de acceso relativa a los recursos estáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="612a0-307">**Clave**: webroot</span><span class="sxs-lookup"><span data-stu-id="612a0-307">**Key**: webroot</span></span>  
<span data-ttu-id="612a0-308">**Tipo**: *cadena*</span><span class="sxs-lookup"><span data-stu-id="612a0-308">**Type**: *string*</span></span>  
<span data-ttu-id="612a0-309">**Predeterminado**: De manera predeterminada, es `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="612a0-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="612a0-310">Debe existir la ruta de acceso a *{raíz del contenido}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="612a0-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="612a0-311">Si la ruta de acceso no existe, se utiliza un proveedor de archivos no-op.</span><span class="sxs-lookup"><span data-stu-id="612a0-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="612a0-312">**Establecer mediante**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="612a0-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="612a0-313">**Variable de entorno**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="612a0-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="612a0-314">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="612a0-314">For more information, see:</span></span>

* [<span data-ttu-id="612a0-315">Aspectos básicos: Raíz web</span><span class="sxs-lookup"><span data-stu-id="612a0-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="612a0-316">Raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="612a0-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="612a0-317">Invalidación de la configuración</span><span class="sxs-lookup"><span data-stu-id="612a0-317">Override configuration</span></span>

<span data-ttu-id="612a0-318">Use [Configuración](xref:fundamentals/configuration/index) para configurar el host web.</span><span class="sxs-lookup"><span data-stu-id="612a0-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="612a0-319">En el ejemplo siguiente, la configuración del host se especifica de forma opcional en un archivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="612a0-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="612a0-320">Cualquier configuración que se cargue del archivo *hostsettings.json* se puede reemplazar por argumentos de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="612a0-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="612a0-321">La configuración generada (en `config`) se usa para configurar el host con [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="612a0-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="612a0-322">La configuración de `IWebHostBuilder` se agrega a la de la aplicación, pero lo contrario no es aplicable: `ConfigureAppConfiguration` no afecta a la configuración de `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="612a0-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="612a0-323">Reemplazar primero la configuración proporcionada por `UseUrls` por la de *hostsettings.json* y, luego, por la del argumento de línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="612a0-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="612a0-324">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="612a0-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="612a0-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) solo copia las claves del elemento `IConfiguration` proporcionado a la configuración del generador de host.</span><span class="sxs-lookup"><span data-stu-id="612a0-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="612a0-326">Por consiguiente, el hecho de configurar `reloadOnChange: true` para los archivos de configuración XML, JSON e INI no tiene ningún efecto.</span><span class="sxs-lookup"><span data-stu-id="612a0-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="612a0-327">Para especificar el host que se ejecuta en una dirección URL determinada, se puede pasar el valor deseado desde un símbolo del sistema al ejecutar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="612a0-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="612a0-328">El argumento de línea de comandos reemplaza el valor `urls` del archivo *hostsettings.json*, y el servidor efectúa la escucha en el puerto 8080:</span><span class="sxs-lookup"><span data-stu-id="612a0-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="612a0-329">Administración del host</span><span class="sxs-lookup"><span data-stu-id="612a0-329">Manage the host</span></span>

<span data-ttu-id="612a0-330">**Run**</span><span class="sxs-lookup"><span data-stu-id="612a0-330">**Run**</span></span>

<span data-ttu-id="612a0-331">El método `Run` inicia la aplicación web y bloquea el subproceso que realiza la llamada hasta que se apague el host:</span><span class="sxs-lookup"><span data-stu-id="612a0-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="612a0-332">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="612a0-332">**Start**</span></span>

<span data-ttu-id="612a0-333">Ejecute el host de manera que se evite un bloqueo mediante una llamada a su método `Start`:</span><span class="sxs-lookup"><span data-stu-id="612a0-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="612a0-334">Si se pasa una lista de direcciones URL al método `Start`, la escucha se produce en las direcciones URL especificadas:</span><span class="sxs-lookup"><span data-stu-id="612a0-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="612a0-335">La aplicación puede inicializar un nuevo host usando los valores preconfigurados de `CreateDefaultBuilder` mediante un método práctico estático.</span><span class="sxs-lookup"><span data-stu-id="612a0-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="612a0-336">Estos métodos inician el servidor sin la salida de la consola y con [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) esperando una interrupción (Ctrl-C/SIGINT o SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="612a0-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="612a0-337">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="612a0-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="612a0-338">Start con `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="612a0-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="612a0-339">Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="612a0-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="612a0-340">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="612a0-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="612a0-341">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="612a0-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="612a0-342">**Start(url de cadena, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="612a0-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="612a0-343">Start con una dirección URL y `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="612a0-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="612a0-344">Produce el mismo resultado que **Start(RequestDelegate app)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="612a0-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="612a0-345">**Start(acción\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="612a0-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="612a0-346">Use una instancia de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar el middleware de enrutamiento:</span><span class="sxs-lookup"><span data-stu-id="612a0-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="612a0-347">Utilice las siguientes solicitudes de explorador con el ejemplo:</span><span class="sxs-lookup"><span data-stu-id="612a0-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="612a0-348">Request</span><span class="sxs-lookup"><span data-stu-id="612a0-348">Request</span></span>                                    | <span data-ttu-id="612a0-349">Respuesta</span><span class="sxs-lookup"><span data-stu-id="612a0-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="612a0-350">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="612a0-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="612a0-351">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="612a0-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="612a0-352">Produce una excepción con la cadena "ooops!"</span><span class="sxs-lookup"><span data-stu-id="612a0-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="612a0-353">Produce una excepción con la cadena "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="612a0-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="612a0-354">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="612a0-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="612a0-355">Hello World!</span><span class="sxs-lookup"><span data-stu-id="612a0-355">Hello World!</span></span>                             |

<span data-ttu-id="612a0-356">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="612a0-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="612a0-357">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="612a0-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="612a0-358">**Start(url de cadena, acción\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="612a0-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="612a0-359">Use una dirección URL y una instancia de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="612a0-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="612a0-360">Produce el mismo resultado que **Start(acción\<IRouteBuilder> routeBuilder)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="612a0-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="612a0-361">**StartWith(acción\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="612a0-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="612a0-362">Proporciona un delegado para configurar `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="612a0-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="612a0-363">Haga una solicitud en el explorador a `http://localhost:5000` para recibir la respuesta "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="612a0-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="612a0-364">`WaitForShutdown` se bloquea hasta que se emite un salto (Ctrl-C/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="612a0-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="612a0-365">La aplicación muestra el mensaje `Console.WriteLine` y espera a que se pulse una tecla para salir.</span><span class="sxs-lookup"><span data-stu-id="612a0-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="612a0-366">**StartWith(url de cadena, acción\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="612a0-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="612a0-367">Proporcione una dirección URL y un delegado para configurar `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="612a0-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="612a0-368">Produce el mismo resultado que **StartWith(acción\<IApplicationBuilder> app)** , excepto que la aplicación responde en `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="612a0-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="612a0-369">Interfaz IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="612a0-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="612a0-370">La interfaz `IWebHostEnvironment` proporciona información sobre el entorno de hospedaje web de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="612a0-371">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IWebHostEnvironment` a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="612a0-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="612a0-372">Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno.</span><span class="sxs-lookup"><span data-stu-id="612a0-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="612a0-373">Como alternativa, inserte `IWebHostEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="612a0-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="612a0-374">Además del método de extensión `IsDevelopment`, `IWebHostEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="612a0-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="612a0-375">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="612a0-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="612a0-376">El servicio `IWebHostEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:</span><span class="sxs-lookup"><span data-stu-id="612a0-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="612a0-377">`IWebHostEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="612a0-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="612a0-378">Interfaz IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="612a0-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="612a0-379">La [interfaz IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) proporciona información sobre el entorno de hospedaje web de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="612a0-380">Use [inserción de constructores](xref:fundamentals/dependency-injection) para obtener `IHostingEnvironment` a fin de utilizar sus propiedades y métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="612a0-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="612a0-381">Puede utilizarse un [enfoque convencional](xref:fundamentals/environments#environment-based-startup-class-and-methods) para configurar la aplicación en el inicio según el entorno.</span><span class="sxs-lookup"><span data-stu-id="612a0-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="612a0-382">Como alternativa, inserte `IHostingEnvironment` en el constructor `Startup` para su uso en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="612a0-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="612a0-383">Además del método de extensión `IsDevelopment`, `IHostingEnvironment` ofrece los métodos `IsStaging`, `IsProduction` y `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="612a0-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="612a0-384">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="612a0-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="612a0-385">El servicio `IHostingEnvironment` también se puede insertar directamente en el método `Configure` para configurar la canalización de procesamiento:</span><span class="sxs-lookup"><span data-stu-id="612a0-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="612a0-386">`IHostingEnvironment` puede insertarse en el método `Invoke` al crear [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="612a0-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="612a0-387">Interfaz IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="612a0-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="612a0-388">`IHostApplicationLifetime` permite actividades posteriores al inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="612a0-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="612a0-389">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="612a0-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="612a0-390">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="612a0-390">Cancellation Token</span></span>    | <span data-ttu-id="612a0-391">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="612a0-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="612a0-392">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="612a0-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="612a0-393">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="612a0-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="612a0-394">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-394">All requests should be processed.</span></span> <span data-ttu-id="612a0-395">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="612a0-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="612a0-396">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="612a0-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="612a0-397">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-397">Requests may still be processing.</span></span> <span data-ttu-id="612a0-398">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="612a0-398">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="612a0-399">`StopApplication` solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="612a0-400">La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="612a0-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="612a0-401">Interfaz IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="612a0-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="612a0-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite actividades posteriores al inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="612a0-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="612a0-403">Hay tres propiedades en la interfaz que son tokens de cancelación usados para registrar métodos `Action` que definen los eventos de inicio y apagado.</span><span class="sxs-lookup"><span data-stu-id="612a0-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="612a0-404">Token de cancelación</span><span class="sxs-lookup"><span data-stu-id="612a0-404">Cancellation Token</span></span>    | <span data-ttu-id="612a0-405">Se desencadena cuando&#8230;</span><span class="sxs-lookup"><span data-stu-id="612a0-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="612a0-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="612a0-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="612a0-407">El host se ha iniciado completamente.</span><span class="sxs-lookup"><span data-stu-id="612a0-407">The host has fully started.</span></span> |
| [<span data-ttu-id="612a0-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="612a0-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="612a0-409">El host está completando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="612a0-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="612a0-410">Deben procesarse todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-410">All requests should be processed.</span></span> <span data-ttu-id="612a0-411">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="612a0-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="612a0-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="612a0-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="612a0-413">El host está realizando un apagado estable.</span><span class="sxs-lookup"><span data-stu-id="612a0-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="612a0-414">Puede que todavía se estén procesando las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="612a0-414">Requests may still be processing.</span></span> <span data-ttu-id="612a0-415">El apagado se bloquea hasta que se complete este evento.</span><span class="sxs-lookup"><span data-stu-id="612a0-415">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="612a0-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita la terminación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="612a0-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="612a0-417">La siguiente clase usa `StopApplication` para cerrar de forma estable una aplicación cuando se llama al método `Shutdown` de esa clase:</span><span class="sxs-lookup"><span data-stu-id="612a0-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="612a0-418">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="612a0-418">Scope validation</span></span>

<span data-ttu-id="612a0-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) establece [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) en `true` si el entorno de la aplicación es desarrollo.</span><span class="sxs-lookup"><span data-stu-id="612a0-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="612a0-420">Cuando `ValidateScopes` está establecido en `true`, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="612a0-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="612a0-421">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="612a0-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="612a0-422">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="612a0-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="612a0-423">El proveedor de servicios raíz se crea cuando se llama a [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="612a0-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="612a0-424">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="612a0-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="612a0-425">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="612a0-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="612a0-426">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="612a0-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="612a0-427">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="612a0-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="612a0-428">Para validar los ámbitos siempre, incluso en el entorno de producción, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) con [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) en el generador de hosts:</span><span class="sxs-lookup"><span data-stu-id="612a0-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="612a0-429">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="612a0-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
