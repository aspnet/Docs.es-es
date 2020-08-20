---
title: Módulo ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar el módulo de ASP.NET Core para hospedar aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 9197f8509141b30dffcc2ccc11979f8853b37d39
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633141"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="99c2f-103">Módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99c2f-103">ASP.NET Core Module</span></span>

<span data-ttu-id="99c2f-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) y [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="99c2f-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99c2f-105">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="99c2f-106">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="99c2f-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="99c2f-107">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="99c2f-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="99c2f-108">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="99c2f-108">Supported Windows versions:</span></span>

* <span data-ttu-id="99c2f-109">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="99c2f-109">Windows 7 or later</span></span>
* <span data-ttu-id="99c2f-110">Windows Server 2012 R2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="99c2f-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="99c2f-111">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="99c2f-112">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="99c2f-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="99c2f-113">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="99c2f-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="99c2f-114">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="99c2f-115">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-115">In-process hosting model</span></span>

<span data-ttu-id="99c2f-116">Las aplicaciones ASP.NET Core usan de forma predeterminada el modelo de hospedaje dentro de proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="99c2f-117">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="99c2f-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="99c2f-118">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="99c2f-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="99c2f-119">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="99c2f-120">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="99c2f-121">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="99c2f-122">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="99c2f-123">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="99c2f-124">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="99c2f-125">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-125">Use one app pool per app.</span></span>

* <span data-ttu-id="99c2f-126">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="99c2f-127">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="99c2f-128">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="99c2f-129">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-129">Client disconnects are detected.</span></span> <span data-ttu-id="99c2f-130">El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="99c2f-131">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="99c2f-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="99c2f-132">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="99c2f-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="99c2f-133">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="99c2f-134">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="99c2f-135">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="99c2f-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="99c2f-136">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="99c2f-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="99c2f-137">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="99c2f-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="99c2f-138">No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).</span><span class="sxs-lookup"><span data-stu-id="99c2f-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="99c2f-139">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-139">Out-of-process hosting model</span></span>

<span data-ttu-id="99c2f-140">Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( *.csproj*):</span><span class="sxs-lookup"><span data-stu-id="99c2f-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="99c2f-141">El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="99c2f-142">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="99c2f-143">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="99c2f-144">Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="99c2f-145">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="99c2f-146">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="99c2f-147">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-147">Hosting model changes</span></span>

<span data-ttu-id="99c2f-148">Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="99c2f-149">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="99c2f-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="99c2f-150">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="99c2f-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="99c2f-151">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-151">Process name</span></span>

<span data-ttu-id="99c2f-152">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="99c2f-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="99c2f-153">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="99c2f-154">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="99c2f-155">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="99c2f-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="99c2f-156">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="99c2f-157">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="99c2f-158">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="99c2f-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="99c2f-159">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99c2f-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="99c2f-160">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="99c2f-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="99c2f-161">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-161">Configuration with web.config</span></span>

<span data-ttu-id="99c2f-162">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="99c2f-163">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="99c2f-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="99c2f-164">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="99c2f-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="99c2f-165">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="99c2f-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="99c2f-166">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-167">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="99c2f-168">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="99c2f-169">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="99c2f-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="99c2f-170">Atributo</span><span class="sxs-lookup"><span data-stu-id="99c2f-170">Attribute</span></span> | <span data-ttu-id="99c2f-171">Descripción</span><span class="sxs-lookup"><span data-stu-id="99c2f-171">Description</span></span> | <span data-ttu-id="99c2f-172">Default</span><span class="sxs-lookup"><span data-stu-id="99c2f-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="99c2f-173">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-173">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-174">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="99c2f-175">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-176">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="99c2f-177">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-178">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="99c2f-179">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="99c2f-180">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-180">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-181">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="99c2f-182">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-183">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="99c2f-184">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="99c2f-185">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="99c2f-186">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="99c2f-187">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-187">Default: `1`</span></span><br><span data-ttu-id="99c2f-188">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-188">Min: `1`</span></span><br><span data-ttu-id="99c2f-189">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="99c2f-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="99c2f-190">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="99c2f-190">Required string attribute.</span></span></p><p><span data-ttu-id="99c2f-191">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="99c2f-192">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-192">Relative paths are supported.</span></span> <span data-ttu-id="99c2f-193">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="99c2f-194">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-195">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="99c2f-196">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="99c2f-197">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="99c2f-198">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-198">Default: `10`</span></span><br><span data-ttu-id="99c2f-199">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-199">Min: `0`</span></span><br><span data-ttu-id="99c2f-200">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="99c2f-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="99c2f-201">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="99c2f-202">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="99c2f-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="99c2f-203">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="99c2f-204">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="99c2f-205">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="99c2f-206">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="99c2f-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="99c2f-207">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="99c2f-208">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-208">Default: `00:02:00`</span></span><br><span data-ttu-id="99c2f-209">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-209">Min: `00:00:00`</span></span><br><span data-ttu-id="99c2f-210">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="99c2f-211">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-212">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="99c2f-213">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-213">Default: `10`</span></span><br><span data-ttu-id="99c2f-214">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-214">Min: `0`</span></span><br><span data-ttu-id="99c2f-215">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="99c2f-216">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-217">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="99c2f-218">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="99c2f-219">Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración **rapidFailsPerMinute**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="99c2f-220">Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="99c2f-221">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="99c2f-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="99c2f-222">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="99c2f-222">Default: `120`</span></span><br><span data-ttu-id="99c2f-223">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-223">Min: `0`</span></span><br><span data-ttu-id="99c2f-224">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="99c2f-225">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-226">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="99c2f-227">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-227">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-228">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="99c2f-229">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="99c2f-230">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="99c2f-231">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-232">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="99c2f-233">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="99c2f-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="99c2f-234">Establecimiento de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="99c2f-234">Set environment variables</span></span>

<span data-ttu-id="99c2f-235">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="99c2f-236">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="99c2f-237">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="99c2f-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="99c2f-238">El ejemplo siguiente establece dos variables de entorno en *web.config*. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación en `Development`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="99c2f-239">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="99c2f-240">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="99c2f-241">Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="99c2f-242">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="99c2f-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="99c2f-243">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="99c2f-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="99c2f-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="99c2f-244">app_offline.htm</span></span>

<span data-ttu-id="99c2f-245">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="99c2f-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="99c2f-246">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="99c2f-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="99c2f-247">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="99c2f-248">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="99c2f-249">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="99c2f-250">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="99c2f-251">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="99c2f-251">Start-up error page</span></span>

<span data-ttu-id="99c2f-252">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="99c2f-253">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-254">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-255">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-256">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="99c2f-257">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="99c2f-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="99c2f-258">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="99c2f-258">Log creation and redirection</span></span>

<span data-ttu-id="99c2f-259">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="99c2f-260">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-261">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="99c2f-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="99c2f-262">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="99c2f-263">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="99c2f-264">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="99c2f-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="99c2f-265">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="99c2f-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="99c2f-266">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="99c2f-267">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="99c2f-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="99c2f-268">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="99c2f-269">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="99c2f-270">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="99c2f-271">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="99c2f-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="99c2f-272">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="99c2f-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="99c2f-273">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="99c2f-274">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="99c2f-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="99c2f-275">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-276">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="99c2f-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="99c2f-277">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="99c2f-278">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="99c2f-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="99c2f-279">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="99c2f-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="99c2f-280">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="99c2f-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="99c2f-281">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="99c2f-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="99c2f-282">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (*logs* en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="99c2f-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-283">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="99c2f-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="99c2f-284">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="99c2f-285">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="99c2f-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="99c2f-286">ERROR</span><span class="sxs-lookup"><span data-stu-id="99c2f-286">ERROR</span></span>
* <span data-ttu-id="99c2f-287">WARNING</span><span class="sxs-lookup"><span data-stu-id="99c2f-287">WARNING</span></span>
* <span data-ttu-id="99c2f-288">INFO</span><span class="sxs-lookup"><span data-stu-id="99c2f-288">INFO</span></span>
* <span data-ttu-id="99c2f-289">TRACE</span><span class="sxs-lookup"><span data-stu-id="99c2f-289">TRACE</span></span>

<span data-ttu-id="99c2f-290">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="99c2f-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="99c2f-291">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="99c2f-291">CONSOLE</span></span>
* <span data-ttu-id="99c2f-292">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="99c2f-292">EVENTLOG</span></span>
* <span data-ttu-id="99c2f-293">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="99c2f-293">FILE</span></span>

<span data-ttu-id="99c2f-294">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="99c2f-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="99c2f-295">`ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="99c2f-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="99c2f-296">(El valor predeterminado es *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="99c2f-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="99c2f-297">`ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="99c2f-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="99c2f-298">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="99c2f-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="99c2f-299">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-299">The size of the log isn't limited.</span></span> <span data-ttu-id="99c2f-300">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="99c2f-301">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="99c2f-302">Modificación del tamaño de la pila</span><span class="sxs-lookup"><span data-stu-id="99c2f-302">Modify the stack size</span></span>

<span data-ttu-id="99c2f-303">*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*</span><span class="sxs-lookup"><span data-stu-id="99c2f-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="99c2f-304">Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en *web.config*. El tamaño predeterminado es `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="99c2f-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="99c2f-305">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="99c2f-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="99c2f-306">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="99c2f-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="99c2f-307">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="99c2f-308">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="99c2f-309">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="99c2f-310">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="99c2f-311">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="99c2f-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="99c2f-312">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="99c2f-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="99c2f-313">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="99c2f-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="99c2f-314">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="99c2f-315">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="99c2f-316">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="99c2f-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="99c2f-317">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="99c2f-318">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="99c2f-319">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="99c2f-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="99c2f-320">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="99c2f-321">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="99c2f-322">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="99c2f-322">Run the installer.</span></span>
1. <span data-ttu-id="99c2f-323">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="99c2f-324">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="99c2f-325">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="99c2f-326">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="99c2f-327">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="99c2f-328">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="99c2f-329">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="99c2f-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="99c2f-330">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="99c2f-331">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="99c2f-332">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="99c2f-333">Module</span><span class="sxs-lookup"><span data-stu-id="99c2f-333">Module</span></span>

<span data-ttu-id="99c2f-334">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-335">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="99c2f-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="99c2f-339">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="99c2f-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="99c2f-344">Schema</span><span class="sxs-lookup"><span data-stu-id="99c2f-344">Schema</span></span>

<span data-ttu-id="99c2f-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-345">**IIS**</span></span>

* <span data-ttu-id="99c2f-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="99c2f-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="99c2f-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-348">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="99c2f-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="99c2f-351">Configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-351">Configuration</span></span>

<span data-ttu-id="99c2f-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-352">**IIS**</span></span>

* <span data-ttu-id="99c2f-353">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="99c2f-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-354">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="99c2f-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="99c2f-357">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="99c2f-358">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="99c2f-359">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="99c2f-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="99c2f-360">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="99c2f-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="99c2f-361">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="99c2f-361">Supported Windows versions:</span></span>

* <span data-ttu-id="99c2f-362">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="99c2f-362">Windows 7 or later</span></span>
* <span data-ttu-id="99c2f-363">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="99c2f-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="99c2f-364">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="99c2f-365">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="99c2f-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="99c2f-366">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="99c2f-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="99c2f-367">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="99c2f-368">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-368">In-process hosting model</span></span>

<span data-ttu-id="99c2f-369">Para configurar una aplicación para el hospedaje en proceso, agregue la propiedad `<AspNetCoreHostingModel>` al archivo de proyecto de la aplicación con un valor de `InProcess` (el hospedaje fuera de proceso se establece con `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="99c2f-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="99c2f-370">No se admite el modelo de hospedaje en proceso para aplicaciones de ASP.NET Core que tienen como destino .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="99c2f-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="99c2f-371">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="99c2f-372">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="99c2f-373">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="99c2f-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="99c2f-374">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="99c2f-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="99c2f-375">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="99c2f-376">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="99c2f-377">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="99c2f-378">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="99c2f-379">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="99c2f-380">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="99c2f-381">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-381">Use one app pool per app.</span></span>

* <span data-ttu-id="99c2f-382">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="99c2f-383">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="99c2f-384">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="99c2f-385">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-385">Client disconnects are detected.</span></span> <span data-ttu-id="99c2f-386">El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="99c2f-387">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="99c2f-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="99c2f-388">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="99c2f-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="99c2f-389">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="99c2f-390">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="99c2f-391">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="99c2f-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="99c2f-392">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="99c2f-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="99c2f-393">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="99c2f-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="99c2f-394">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-394">Out-of-process hosting model</span></span>

<span data-ttu-id="99c2f-395">Para configurar una aplicación para el hospedaje fuera de proceso, use uno de los métodos siguientes en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="99c2f-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="99c2f-396">No especifique la propiedad `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="99c2f-397">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="99c2f-398">Establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` (el hospedaje en proceso se establece con `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="99c2f-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="99c2f-399">El valor no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="99c2f-400">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="99c2f-401">Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="99c2f-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="99c2f-402">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="99c2f-403">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="99c2f-404">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-404">Hosting model changes</span></span>

<span data-ttu-id="99c2f-405">Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="99c2f-406">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="99c2f-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="99c2f-407">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="99c2f-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="99c2f-408">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="99c2f-408">Process name</span></span>

<span data-ttu-id="99c2f-409">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="99c2f-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="99c2f-410">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="99c2f-411">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="99c2f-412">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="99c2f-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="99c2f-413">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="99c2f-414">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="99c2f-415">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="99c2f-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="99c2f-416">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99c2f-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="99c2f-417">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="99c2f-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="99c2f-418">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-418">Configuration with web.config</span></span>

<span data-ttu-id="99c2f-419">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="99c2f-420">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="99c2f-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="99c2f-421">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="99c2f-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="99c2f-422">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="99c2f-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="99c2f-423">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-424">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="99c2f-425">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="99c2f-426">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="99c2f-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="99c2f-427">Atributo</span><span class="sxs-lookup"><span data-stu-id="99c2f-427">Attribute</span></span> | <span data-ttu-id="99c2f-428">Descripción</span><span class="sxs-lookup"><span data-stu-id="99c2f-428">Description</span></span> | <span data-ttu-id="99c2f-429">Default</span><span class="sxs-lookup"><span data-stu-id="99c2f-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="99c2f-430">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-430">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-431">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="99c2f-432">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-433">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="99c2f-434">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-435">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="99c2f-436">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="99c2f-437">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-437">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-438">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="99c2f-439">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-440">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="99c2f-441">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="99c2f-442">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="99c2f-443">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="99c2f-444">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-444">Default: `1`</span></span><br><span data-ttu-id="99c2f-445">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-445">Min: `1`</span></span><br><span data-ttu-id="99c2f-446">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="99c2f-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="99c2f-447">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="99c2f-447">Required string attribute.</span></span></p><p><span data-ttu-id="99c2f-448">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="99c2f-449">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-449">Relative paths are supported.</span></span> <span data-ttu-id="99c2f-450">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="99c2f-451">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-452">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="99c2f-453">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="99c2f-454">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="99c2f-455">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-455">Default: `10`</span></span><br><span data-ttu-id="99c2f-456">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-456">Min: `0`</span></span><br><span data-ttu-id="99c2f-457">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="99c2f-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="99c2f-458">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="99c2f-459">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="99c2f-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="99c2f-460">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="99c2f-461">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="99c2f-462">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="99c2f-463">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="99c2f-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="99c2f-464">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="99c2f-465">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-465">Default: `00:02:00`</span></span><br><span data-ttu-id="99c2f-466">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-466">Min: `00:00:00`</span></span><br><span data-ttu-id="99c2f-467">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="99c2f-468">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-469">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="99c2f-470">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-470">Default: `10`</span></span><br><span data-ttu-id="99c2f-471">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-471">Min: `0`</span></span><br><span data-ttu-id="99c2f-472">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="99c2f-473">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-474">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="99c2f-475">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="99c2f-476">Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración **rapidFailsPerMinute**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="99c2f-477">Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="99c2f-478">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="99c2f-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="99c2f-479">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="99c2f-479">Default: `120`</span></span><br><span data-ttu-id="99c2f-480">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-480">Min: `0`</span></span><br><span data-ttu-id="99c2f-481">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="99c2f-482">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-483">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="99c2f-484">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-484">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-485">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="99c2f-486">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="99c2f-487">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="99c2f-488">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-489">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="99c2f-490">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="99c2f-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="99c2f-491">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="99c2f-491">Setting environment variables</span></span>

<span data-ttu-id="99c2f-492">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="99c2f-493">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="99c2f-494">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="99c2f-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="99c2f-495">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="99c2f-495">The following example sets two environment variables.</span></span> <span data-ttu-id="99c2f-496">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="99c2f-497">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="99c2f-498">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="99c2f-499">Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="99c2f-500">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="99c2f-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="99c2f-501">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="99c2f-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="99c2f-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="99c2f-502">app_offline.htm</span></span>

<span data-ttu-id="99c2f-503">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="99c2f-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="99c2f-504">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="99c2f-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="99c2f-505">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="99c2f-506">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="99c2f-507">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="99c2f-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="99c2f-508">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="99c2f-509">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="99c2f-509">Start-up error page</span></span>

<span data-ttu-id="99c2f-510">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="99c2f-511">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-512">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-513">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="99c2f-514">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="99c2f-515">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="99c2f-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="99c2f-516">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="99c2f-516">Log creation and redirection</span></span>

<span data-ttu-id="99c2f-517">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="99c2f-518">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-519">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="99c2f-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="99c2f-520">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="99c2f-521">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="99c2f-522">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="99c2f-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="99c2f-523">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="99c2f-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="99c2f-524">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="99c2f-525">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="99c2f-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="99c2f-526">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="99c2f-527">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="99c2f-528">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="99c2f-529">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="99c2f-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="99c2f-530">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="99c2f-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="99c2f-531">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="99c2f-532">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="99c2f-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="99c2f-533">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-534">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="99c2f-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="99c2f-535">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="99c2f-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="99c2f-536">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="99c2f-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="99c2f-537">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="99c2f-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="99c2f-538">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="99c2f-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="99c2f-539">El módulo no crea automáticamente las carpetas de la ruta de acceso proporcionada al valor `<handlerSetting>` (*logs* en el ejemplo anterior), que deben existir previamente en la implementación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="99c2f-540">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="99c2f-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="99c2f-541">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="99c2f-542">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="99c2f-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="99c2f-543">ERROR</span><span class="sxs-lookup"><span data-stu-id="99c2f-543">ERROR</span></span>
* <span data-ttu-id="99c2f-544">WARNING</span><span class="sxs-lookup"><span data-stu-id="99c2f-544">WARNING</span></span>
* <span data-ttu-id="99c2f-545">INFO</span><span class="sxs-lookup"><span data-stu-id="99c2f-545">INFO</span></span>
* <span data-ttu-id="99c2f-546">TRACE</span><span class="sxs-lookup"><span data-stu-id="99c2f-546">TRACE</span></span>

<span data-ttu-id="99c2f-547">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="99c2f-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="99c2f-548">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="99c2f-548">CONSOLE</span></span>
* <span data-ttu-id="99c2f-549">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="99c2f-549">EVENTLOG</span></span>
* <span data-ttu-id="99c2f-550">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="99c2f-550">FILE</span></span>

<span data-ttu-id="99c2f-551">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="99c2f-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="99c2f-552">`ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="99c2f-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="99c2f-553">(El valor predeterminado es *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="99c2f-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="99c2f-554">`ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="99c2f-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="99c2f-555">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="99c2f-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="99c2f-556">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-556">The size of the log isn't limited.</span></span> <span data-ttu-id="99c2f-557">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="99c2f-558">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="99c2f-559">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="99c2f-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="99c2f-560">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="99c2f-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="99c2f-561">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="99c2f-562">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="99c2f-563">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="99c2f-564">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="99c2f-565">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="99c2f-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="99c2f-566">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="99c2f-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="99c2f-567">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="99c2f-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="99c2f-568">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="99c2f-569">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="99c2f-570">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="99c2f-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="99c2f-571">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="99c2f-572">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="99c2f-573">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="99c2f-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="99c2f-574">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="99c2f-575">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="99c2f-576">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="99c2f-576">Run the installer.</span></span>
1. <span data-ttu-id="99c2f-577">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="99c2f-578">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="99c2f-579">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="99c2f-580">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="99c2f-581">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="99c2f-582">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="99c2f-583">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="99c2f-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="99c2f-584">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="99c2f-585">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="99c2f-586">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="99c2f-587">Module</span><span class="sxs-lookup"><span data-stu-id="99c2f-587">Module</span></span>

<span data-ttu-id="99c2f-588">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-589">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="99c2f-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="99c2f-593">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="99c2f-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="99c2f-598">Schema</span><span class="sxs-lookup"><span data-stu-id="99c2f-598">Schema</span></span>

<span data-ttu-id="99c2f-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-599">**IIS**</span></span>

* <span data-ttu-id="99c2f-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="99c2f-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="99c2f-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-602">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="99c2f-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="99c2f-605">Configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-605">Configuration</span></span>

<span data-ttu-id="99c2f-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-606">**IIS**</span></span>

* <span data-ttu-id="99c2f-607">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="99c2f-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-608">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="99c2f-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="99c2f-611">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="99c2f-612">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para reenviar solicitudes web a aplicaciones ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="99c2f-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="99c2f-613">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="99c2f-613">Supported Windows versions:</span></span>

* <span data-ttu-id="99c2f-614">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="99c2f-614">Windows 7 or later</span></span>
* <span data-ttu-id="99c2f-615">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="99c2f-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="99c2f-616">El módulo funciona únicamente con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="99c2f-616">The module only works with Kestrel.</span></span> <span data-ttu-id="99c2f-617">El módulo no es compatible con [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="99c2f-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="99c2f-618">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo también se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="99c2f-619">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud, y reinicia la aplicación si esta se bloquea.</span><span class="sxs-lookup"><span data-stu-id="99c2f-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="99c2f-620">Este comportamiento es básicamente el mismo que el de las aplicaciones ASP.NET 4.x que se ejecutan en el proceso en IIS y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="99c2f-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="99c2f-621">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación:</span><span class="sxs-lookup"><span data-stu-id="99c2f-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="99c2f-623">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="99c2f-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="99c2f-624">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="99c2f-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="99c2f-625">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="99c2f-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="99c2f-626">El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="99c2f-627">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="99c2f-628">El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="99c2f-629">Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="99c2f-630">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="99c2f-631">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="99c2f-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="99c2f-632">La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="99c2f-633">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="99c2f-634">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="99c2f-635">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="99c2f-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="99c2f-636">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="99c2f-637">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="99c2f-638">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="99c2f-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="99c2f-639">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99c2f-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="99c2f-640">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="99c2f-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="99c2f-641">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-641">Configuration with web.config</span></span>

<span data-ttu-id="99c2f-642">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="99c2f-643">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="99c2f-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="99c2f-644">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="99c2f-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="99c2f-645">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-646">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="99c2f-647">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="99c2f-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="99c2f-648">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="99c2f-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="99c2f-649">Atributo</span><span class="sxs-lookup"><span data-stu-id="99c2f-649">Attribute</span></span> | <span data-ttu-id="99c2f-650">Descripción</span><span class="sxs-lookup"><span data-stu-id="99c2f-650">Description</span></span> | <span data-ttu-id="99c2f-651">Default</span><span class="sxs-lookup"><span data-stu-id="99c2f-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="99c2f-652">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-652">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-653">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="99c2f-654">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-655">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="99c2f-656">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-657">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="99c2f-658">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="99c2f-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="99c2f-659">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-660">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="99c2f-661">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="99c2f-662">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="99c2f-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="99c2f-663">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-663">Default: `1`</span></span><br><span data-ttu-id="99c2f-664">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="99c2f-664">Min: `1`</span></span><br><span data-ttu-id="99c2f-665">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="99c2f-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="99c2f-666">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="99c2f-666">Required string attribute.</span></span></p><p><span data-ttu-id="99c2f-667">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="99c2f-668">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-668">Relative paths are supported.</span></span> <span data-ttu-id="99c2f-669">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="99c2f-670">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-671">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="99c2f-672">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="99c2f-673">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-673">Default: `10`</span></span><br><span data-ttu-id="99c2f-674">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-674">Min: `0`</span></span><br><span data-ttu-id="99c2f-675">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="99c2f-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="99c2f-676">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="99c2f-677">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="99c2f-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="99c2f-678">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="99c2f-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="99c2f-679">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-679">Default: `00:02:00`</span></span><br><span data-ttu-id="99c2f-680">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-680">Min: `00:00:00`</span></span><br><span data-ttu-id="99c2f-681">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="99c2f-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="99c2f-682">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-683">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="99c2f-684">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="99c2f-684">Default: `10`</span></span><br><span data-ttu-id="99c2f-685">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-685">Min: `0`</span></span><br><span data-ttu-id="99c2f-686">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="99c2f-687">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="99c2f-688">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="99c2f-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="99c2f-689">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="99c2f-690">El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="99c2f-691">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="99c2f-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="99c2f-692">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="99c2f-692">Default: `120`</span></span><br><span data-ttu-id="99c2f-693">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="99c2f-693">Min: `0`</span></span><br><span data-ttu-id="99c2f-694">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="99c2f-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="99c2f-695">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="99c2f-696">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="99c2f-697">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="99c2f-697">Optional string attribute.</span></span></p><p><span data-ttu-id="99c2f-698">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="99c2f-699">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="99c2f-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="99c2f-700">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="99c2f-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="99c2f-701">Las carpetas que se proporcionan en la ruta de acceso deben estar en orden para que el módulo cree el archivo de registro.</span><span class="sxs-lookup"><span data-stu-id="99c2f-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="99c2f-702">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="99c2f-703">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="99c2f-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="99c2f-704">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="99c2f-704">Setting environment variables</span></span>

<span data-ttu-id="99c2f-705">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="99c2f-706">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="99c2f-707">Las variables de entorno establecidas en esta sección entran en conflicto con las variables de entorno del sistema que tienen el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="99c2f-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="99c2f-708">Si se establece una variable de entorno en el archivo *web.config* y en el nivel del sistema en Windows, el valor del archivo *web.config* se anexa al valor de la variable de entorno del sistema (por ejemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), que impide que se inicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="99c2f-709">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="99c2f-709">The following example sets two environment variables.</span></span> <span data-ttu-id="99c2f-710">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="99c2f-711">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="99c2f-712">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="99c2f-713">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="99c2f-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="99c2f-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="99c2f-714">app_offline.htm</span></span>

<span data-ttu-id="99c2f-715">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="99c2f-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="99c2f-716">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="99c2f-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="99c2f-717">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="99c2f-718">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="99c2f-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="99c2f-719">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="99c2f-719">Start-up error page</span></span>

<span data-ttu-id="99c2f-720">Si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparece una página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="99c2f-721">Para suprimir esta página y volver a la página de código de estado 502 de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="99c2f-722">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="99c2f-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de códigos de estado 502.5 Error de proceso](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="99c2f-724">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="99c2f-724">Log creation and redirection</span></span>

<span data-ttu-id="99c2f-725">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="99c2f-726">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="99c2f-727">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="99c2f-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="99c2f-728">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="99c2f-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="99c2f-729">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="99c2f-730">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="99c2f-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="99c2f-731">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="99c2f-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="99c2f-732">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="99c2f-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="99c2f-733">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="99c2f-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="99c2f-734">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="99c2f-735">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="99c2f-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="99c2f-736">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="99c2f-737">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="99c2f-738">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="99c2f-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="99c2f-739">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="99c2f-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="99c2f-740">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="99c2f-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="99c2f-741">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99c2f-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="99c2f-742">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="99c2f-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="99c2f-743">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="99c2f-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="99c2f-744">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="99c2f-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="99c2f-745">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="99c2f-746">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="99c2f-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="99c2f-747">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="99c2f-748">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="99c2f-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="99c2f-749">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="99c2f-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="99c2f-750">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="99c2f-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="99c2f-751">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="99c2f-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="99c2f-752">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="99c2f-753">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="99c2f-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="99c2f-754">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="99c2f-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="99c2f-755">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="99c2f-756">Al usar una configuración compartida de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="99c2f-757">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="99c2f-758">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="99c2f-758">Run the installer.</span></span>
1. <span data-ttu-id="99c2f-759">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="99c2f-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="99c2f-760">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="99c2f-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="99c2f-761">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="99c2f-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="99c2f-762">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="99c2f-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="99c2f-763">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="99c2f-764">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="99c2f-765">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="99c2f-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="99c2f-766">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="99c2f-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="99c2f-767">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="99c2f-768">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="99c2f-769">Module</span><span class="sxs-lookup"><span data-stu-id="99c2f-769">Module</span></span>

<span data-ttu-id="99c2f-770">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-771">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="99c2f-773">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="99c2f-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="99c2f-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="99c2f-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="99c2f-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="99c2f-776">Schema</span><span class="sxs-lookup"><span data-stu-id="99c2f-776">Schema</span></span>

<span data-ttu-id="99c2f-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-777">**IIS**</span></span>

* <span data-ttu-id="99c2f-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="99c2f-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-779">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="99c2f-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="99c2f-781">Configuración</span><span class="sxs-lookup"><span data-stu-id="99c2f-781">Configuration</span></span>

<span data-ttu-id="99c2f-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="99c2f-782">**IIS**</span></span>

* <span data-ttu-id="99c2f-783">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="99c2f-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="99c2f-784">**IIS Express**</span></span>

* <span data-ttu-id="99c2f-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="99c2f-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="99c2f-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="99c2f-787">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="99c2f-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="99c2f-788">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="99c2f-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="99c2f-789">[Origen de referencia del módulo ASP.NET Core (rama maestra)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): use la lista desplegable **Rama** para seleccionar una versión específica (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="99c2f-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
