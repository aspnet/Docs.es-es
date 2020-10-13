---
title: Módulo ASP.NET Core
author: rick-anderson
description: Obtenga información del módulo de ASP.NET Core para hospedar aplicaciones de ASP.NET Core con IIS.
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
ms.openlocfilehash: 8ee9ab2b598bc8ff62faa45a5666615ee7ab239b
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754676"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="4596e-103">Módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4596e-103">ASP.NET Core Module</span></span>

<span data-ttu-id="4596e-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) y [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="4596e-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4596e-105">El módulo de ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS, lo que permite que las aplicaciones de ASP.NET Core funcionen con IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="4596e-106">Para ejecutar las aplicaciones de ASP.NET Core con IIS puede:</span><span class="sxs-lookup"><span data-stu-id="4596e-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="4596e-107">Hospedar una aplicación de ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](xref:host-and-deploy/iis/in-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="4596e-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="4596e-108">Reenviar solicitudes web a una aplicación de ASP.NET Core de back-end que ejecuta el servidor de Kestrel, lo que se denomina [modelo de hospedaje fuera de proceso](xref:host-and-deploy/iis/out-of-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="4596e-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="4596e-109">Existen ventajas y desventajas entre cada uno de los modelos de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="4596e-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="4596e-110">De manera predeterminada, se usa el modelo de hospedaje en proceso por el mejor rendimiento y diagnóstico que ofrece.</span><span class="sxs-lookup"><span data-stu-id="4596e-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="4596e-111">Instalación del módulo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4596e-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="4596e-112">Descargue al instalador mediante el vínculo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4596e-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="4596e-113">Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)</span><span class="sxs-lookup"><span data-stu-id="4596e-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="4596e-114">Para obtener más detalles e instrucciones sobre cómo instalar y usar el módulo de ASP.NET Core, o bien sobre cómo instalar versiones distintas del módulo, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4596e-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="4596e-115">Para obtener una experiencia de tutorial en la publicación de una aplicación de ASP.NET Core en un servidor IIS, vea <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="4596e-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4596e-116">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="4596e-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="4596e-117">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4596e-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="4596e-118">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4596e-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="4596e-119">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="4596e-119">Supported Windows versions:</span></span>

* <span data-ttu-id="4596e-120">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="4596e-120">Windows 7 or later</span></span>
* <span data-ttu-id="4596e-121">Windows Server 2012 R2 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="4596e-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="4596e-122">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4596e-123">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4596e-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="4596e-124">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4596e-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4596e-125">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4596e-126">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-126">In-process hosting model</span></span>

<span data-ttu-id="4596e-127">Las aplicaciones ASP.NET Core usan de forma predeterminada el modelo de hospedaje dentro de proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="4596e-128">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="4596e-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="4596e-129">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="4596e-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="4596e-130">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="4596e-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="4596e-131">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="4596e-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="4596e-132">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="4596e-133">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="4596e-134">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="4596e-135">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4596e-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="4596e-136">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-136">Use one app pool per app.</span></span>

* <span data-ttu-id="4596e-137">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo `app_offline.htm` en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="4596e-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4596e-138">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="4596e-139">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4596e-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="4596e-140">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="4596e-140">Client disconnects are detected.</span></span> <span data-ttu-id="4596e-141">El token de cancelación de [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4596e-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="4596e-142">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, `C:\Windows\System32\inetsrv` para `w3wp.exe`).</span><span class="sxs-lookup"><span data-stu-id="4596e-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="4596e-143">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase `CurrentDirectoryHelpers`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="4596e-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="4596e-144">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="4596e-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="4596e-145">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="4596e-146">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="4596e-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="4596e-147">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4596e-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="4596e-148">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="4596e-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="4596e-149">No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).</span><span class="sxs-lookup"><span data-stu-id="4596e-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4596e-150">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-150">Out-of-process hosting model</span></span>

<span data-ttu-id="4596e-151">Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( `.csproj`):</span><span class="sxs-lookup"><span data-stu-id="4596e-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4596e-152">El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="4596e-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="4596e-153">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="4596e-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4596e-154">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4596e-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4596e-155">Fuera del proceso, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="4596e-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="4596e-156">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="4596e-157">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="4596e-158">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-158">Hosting model changes</span></span>

<span data-ttu-id="4596e-159">Si se modifica el valor `hostingModel` en el archivo `web.config` (se explica en la sección [Configuración con `web.config`](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="4596e-160">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="4596e-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="4596e-161">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4596e-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="4596e-162">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-162">Process name</span></span>

<span data-ttu-id="4596e-163">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="4596e-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="4596e-164">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="4596e-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4596e-165">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4596e-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4596e-166">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="4596e-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4596e-167">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="4596e-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4596e-168">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4596e-169">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4596e-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4596e-170">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4596e-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4596e-171">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4596e-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4596e-172">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="4596e-172">Configuration with web.config</span></span>

<span data-ttu-id="4596e-173">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4596e-174">El siguiente archivo `web.config` se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="4596e-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="4596e-175">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="4596e-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="4596e-176">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="4596e-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="4596e-177">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-178">La ruta de acceso guarda los registros de stdout en la carpeta `LogFiles`, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="4596e-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4596e-179">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4596e-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4596e-180">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4596e-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4596e-181">Atributo</span><span class="sxs-lookup"><span data-stu-id="4596e-181">Attribute</span></span> | <span data-ttu-id="4596e-182">Descripción</span><span class="sxs-lookup"><span data-stu-id="4596e-182">Description</span></span> | <span data-ttu-id="4596e-183">Default</span><span class="sxs-lookup"><span data-stu-id="4596e-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4596e-184">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-184">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-185">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4596e-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4596e-186">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-187">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="4596e-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4596e-188">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-189">Si es "true", el token se reenvía al proceso secundario que escucha en `%ASPNETCORE_PORT%` como encabezado `'MS-ASPNETCORE-WINAUTHTOKEN'` por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="4596e-190">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="4596e-191">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-191">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-192">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="4596e-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="4596e-193">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-194">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4596e-195">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="4596e-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="4596e-196">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="4596e-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4596e-197">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="4596e-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4596e-198">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-198">Default: `1`</span></span><br><span data-ttu-id="4596e-199">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-199">Min: `1`</span></span><br><span data-ttu-id="4596e-200">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="4596e-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="4596e-201">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="4596e-201">Required string attribute.</span></span></p><p><span data-ttu-id="4596e-202">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4596e-203">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="4596e-203">Relative paths are supported.</span></span> <span data-ttu-id="4596e-204">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4596e-205">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-206">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="4596e-207">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="4596e-208">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="4596e-209">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-209">Default: `10`</span></span><br><span data-ttu-id="4596e-210">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-210">Min: `0`</span></span><br><span data-ttu-id="4596e-211">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="4596e-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4596e-212">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4596e-213">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="4596e-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4596e-214">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="4596e-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="4596e-215">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="4596e-216">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="4596e-217">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="4596e-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="4596e-218">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="4596e-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="4596e-219">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-219">Default: `00:02:00`</span></span><br><span data-ttu-id="4596e-220">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-220">Min: `00:00:00`</span></span><br><span data-ttu-id="4596e-221">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4596e-222">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-223">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4596e-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="4596e-224">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-224">Default: `10`</span></span><br><span data-ttu-id="4596e-225">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-225">Min: `0`</span></span><br><span data-ttu-id="4596e-226">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="4596e-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4596e-227">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-228">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="4596e-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4596e-229">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="4596e-230">Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración **rapidFailsPerMinute**.</span><span class="sxs-lookup"><span data-stu-id="4596e-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="4596e-231">Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="4596e-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4596e-232">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="4596e-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4596e-233">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="4596e-233">Default: `120`</span></span><br><span data-ttu-id="4596e-234">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-234">Min: `0`</span></span><br><span data-ttu-id="4596e-235">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="4596e-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4596e-236">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-237">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4596e-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4596e-238">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-238">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-239">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4596e-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="4596e-240">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4596e-241">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="4596e-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4596e-242">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-243">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( `.log`) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4596e-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="4596e-244">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como `stdout_20180205194132_1934.log` en la carpeta `logs`, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="4596e-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="4596e-245">Establecimiento de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="4596e-245">Set environment variables</span></span>

<span data-ttu-id="4596e-246">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4596e-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4596e-247">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4596e-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="4596e-248">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="4596e-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="4596e-249">En el ejemplo siguiente se establecen dos variables de entorno en `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="4596e-250">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="4596e-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4596e-251">Un desarrollador puede establecer temporalmente este valor en el archivo `web.config` con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4596e-252">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="4596e-253">Una alternativa a establecer directamente el entorno en `web.config` consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="4596e-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4596e-254">Este método establece el entorno en `web.config` cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="4596e-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="4596e-255">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="4596e-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="4596e-256">Si un archivo con el nombre `app_offline.htm` se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="4596e-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4596e-257">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="4596e-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4596e-258">Mientras el archivo `app_offline.htm` existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4596e-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="4596e-259">Cuando se quita el archivo `app_offline.htm`, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="4596e-260">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="4596e-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4596e-261">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4596e-262">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="4596e-262">Start-up error page</span></span>

<span data-ttu-id="4596e-263">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="4596e-264">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="4596e-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-265">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="4596e-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-266">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="4596e-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-267">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4596e-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4596e-268">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP`<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4596e-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4596e-269">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="4596e-269">Log creation and redirection</span></span>

<span data-ttu-id="4596e-270">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4596e-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4596e-271">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="4596e-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-272">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="4596e-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="4596e-273">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4596e-274">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4596e-275">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4596e-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4596e-276">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="4596e-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4596e-277">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4596e-278">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4596e-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4596e-279">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="4596e-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4596e-280">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente `stdout`) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="4596e-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="4596e-281">Si la ruta de acceso de `stdoutLogFile` finaliza con `stdout`, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="4596e-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="4596e-282">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="4596e-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="4596e-283">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="4596e-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="4596e-284">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4596e-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4596e-285">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="4596e-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="4596e-286">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-287">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="4596e-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4596e-288">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="4596e-289">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4596e-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="4596e-290">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="4596e-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="4596e-291">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="4596e-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="4596e-292">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` en `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="4596e-293">Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="4596e-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="4596e-294">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (`logs` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="4596e-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-295">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}`, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones, para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="4596e-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="4596e-296">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="4596e-297">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="4596e-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="4596e-298">ERROR</span><span class="sxs-lookup"><span data-stu-id="4596e-298">ERROR</span></span>
* <span data-ttu-id="4596e-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="4596e-299">WARNING</span></span>
* <span data-ttu-id="4596e-300">INFO</span><span class="sxs-lookup"><span data-stu-id="4596e-300">INFO</span></span>
* <span data-ttu-id="4596e-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="4596e-301">TRACE</span></span>

<span data-ttu-id="4596e-302">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="4596e-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="4596e-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="4596e-303">CONSOLE</span></span>
* <span data-ttu-id="4596e-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="4596e-304">EVENTLOG</span></span>
* <span data-ttu-id="4596e-305">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="4596e-305">FILE</span></span>

<span data-ttu-id="4596e-306">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="4596e-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="4596e-307">`ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="4596e-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="4596e-308">(Valor predeterminado: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="4596e-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="4596e-309">`ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="4596e-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="4596e-310">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="4596e-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="4596e-311">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="4596e-311">The size of the log isn't limited.</span></span> <span data-ttu-id="4596e-312">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="4596e-313">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="4596e-314">Modificación del tamaño de la pila</span><span class="sxs-lookup"><span data-stu-id="4596e-314">Modify the stack size</span></span>

<span data-ttu-id="4596e-315">*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*</span><span class="sxs-lookup"><span data-stu-id="4596e-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="4596e-316">Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="4596e-317">El tamaño predeterminado es de 1 048 576 bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="4596e-317">The default size is 1,048,576 bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4596e-318">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="4596e-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4596e-319">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="4596e-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="4596e-320">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4596e-321">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4596e-322">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="4596e-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4596e-323">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4596e-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4596e-324">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="4596e-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4596e-325">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="4596e-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4596e-326">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="4596e-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4596e-327">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4596e-328">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4596e-329">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="4596e-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4596e-330">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="4596e-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="4596e-331">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo `applicationHost.config` del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="4596e-332">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="4596e-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="4596e-333">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="4596e-334">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4596e-335">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="4596e-335">Run the installer.</span></span>
1. <span data-ttu-id="4596e-336">Exporte el archivo `applicationHost.config` actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="4596e-337">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4596e-338">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4596e-339">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4596e-340">En el sistema de hospedaje, vaya a `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="4596e-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="4596e-341">Busque el archivo `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="4596e-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="4596e-342">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="4596e-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4596e-343">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="4596e-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4596e-344">Los registros del instalador del conjunto de hospedaje para el módulo se encuentran en `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="4596e-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="4596e-345">El archivo se denomina `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span><span class="sxs-lookup"><span data-stu-id="4596e-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4596e-346">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4596e-347">Module</span><span class="sxs-lookup"><span data-stu-id="4596e-347">Module</span></span>

<span data-ttu-id="4596e-348">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4596e-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="4596e-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4596e-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="4596e-350">Schema</span><span class="sxs-lookup"><span data-stu-id="4596e-350">Schema</span></span>

<span data-ttu-id="4596e-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="4596e-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="4596e-353">Configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-353">Configuration</span></span>

<span data-ttu-id="4596e-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="4596e-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-355">**IIS Express**</span></span>

* <span data-ttu-id="4596e-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="4596e-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="4596e-357">CLI de *iisexpress.exe*: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="4596e-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="4596e-358">Los archivos se pueden encontrar mediante la búsqueda de `aspnetcore` en el archivo `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4596e-359">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="4596e-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="4596e-360">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4596e-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="4596e-361">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4596e-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="4596e-362">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="4596e-362">Supported Windows versions:</span></span>

* <span data-ttu-id="4596e-363">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="4596e-363">Windows 7 or later</span></span>
* <span data-ttu-id="4596e-364">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="4596e-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4596e-365">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4596e-366">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4596e-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="4596e-367">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4596e-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4596e-368">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4596e-369">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-369">In-process hosting model</span></span>

<span data-ttu-id="4596e-370">Para configurar una aplicación para el hospedaje en proceso, agregue la propiedad `<AspNetCoreHostingModel>` al archivo de proyecto de la aplicación con un valor de `InProcess` (el hospedaje fuera de proceso se establece con `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="4596e-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4596e-371">No se admite el modelo de hospedaje en proceso para aplicaciones de ASP.NET Core que tienen como destino .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4596e-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="4596e-372">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="4596e-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4596e-373">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="4596e-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="4596e-374">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="4596e-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="4596e-375">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="4596e-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="4596e-376">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="4596e-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="4596e-377">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="4596e-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="4596e-378">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="4596e-379">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="4596e-380">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="4596e-381">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4596e-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="4596e-382">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-382">Use one app pool per app.</span></span>

* <span data-ttu-id="4596e-383">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="4596e-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4596e-384">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="4596e-385">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="4596e-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="4596e-386">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="4596e-386">Client disconnects are detected.</span></span> <span data-ttu-id="4596e-387">El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4596e-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="4596e-388">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="4596e-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="4596e-389">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="4596e-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="4596e-390">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="4596e-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="4596e-391">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="4596e-392">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="4596e-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="4596e-393">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4596e-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="4596e-394">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="4596e-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4596e-395">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-395">Out-of-process hosting model</span></span>

<span data-ttu-id="4596e-396">Para configurar una aplicación para el hospedaje fuera de proceso, use uno de los métodos siguientes en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="4596e-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="4596e-397">No especifique la propiedad `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="4596e-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="4596e-398">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="4596e-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="4596e-399">Establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` (el hospedaje en proceso se establece con `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="4596e-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4596e-400">El valor no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="4596e-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4596e-401">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4596e-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4596e-402">Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="4596e-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="4596e-403">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="4596e-404">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="4596e-405">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-405">Hosting model changes</span></span>

<span data-ttu-id="4596e-406">Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="4596e-407">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="4596e-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="4596e-408">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4596e-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="4596e-409">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="4596e-409">Process name</span></span>

<span data-ttu-id="4596e-410">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="4596e-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="4596e-411">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="4596e-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4596e-412">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4596e-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4596e-413">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="4596e-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4596e-414">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="4596e-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4596e-415">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4596e-416">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4596e-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4596e-417">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4596e-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4596e-418">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4596e-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4596e-419">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="4596e-419">Configuration with web.config</span></span>

<span data-ttu-id="4596e-420">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4596e-421">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="4596e-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="4596e-422">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="4596e-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="4596e-423">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="4596e-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="4596e-424">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-425">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="4596e-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4596e-426">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4596e-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4596e-427">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4596e-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4596e-428">Atributo</span><span class="sxs-lookup"><span data-stu-id="4596e-428">Attribute</span></span> | <span data-ttu-id="4596e-429">Descripción</span><span class="sxs-lookup"><span data-stu-id="4596e-429">Description</span></span> | <span data-ttu-id="4596e-430">Default</span><span class="sxs-lookup"><span data-stu-id="4596e-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4596e-431">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-431">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-432">Argumentos para el archivo ejecutable especificado en `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4596e-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4596e-433">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-434">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="4596e-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4596e-435">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-436">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="4596e-437">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="4596e-438">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-438">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-439">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="4596e-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="4596e-440">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-441">Especifica el número de instancias del proceso especificado en el valor `processPath` que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4596e-442">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="4596e-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="4596e-443">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="4596e-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4596e-444">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="4596e-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4596e-445">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-445">Default: `1`</span></span><br><span data-ttu-id="4596e-446">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-446">Min: `1`</span></span><br><span data-ttu-id="4596e-447">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="4596e-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="4596e-448">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="4596e-448">Required string attribute.</span></span></p><p><span data-ttu-id="4596e-449">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4596e-450">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="4596e-450">Relative paths are supported.</span></span> <span data-ttu-id="4596e-451">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4596e-452">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-453">Especifica el número de veces que el proceso indicado en `processPath` puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="4596e-454">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="4596e-455">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="4596e-456">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-456">Default: `10`</span></span><br><span data-ttu-id="4596e-457">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-457">Min: `0`</span></span><br><span data-ttu-id="4596e-458">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="4596e-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4596e-459">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4596e-460">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="4596e-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4596e-461">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="4596e-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="4596e-462">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="4596e-463">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="4596e-464">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="4596e-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="4596e-465">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="4596e-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="4596e-466">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-466">Default: `00:02:00`</span></span><br><span data-ttu-id="4596e-467">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-467">Min: `00:00:00`</span></span><br><span data-ttu-id="4596e-468">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4596e-469">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-470">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4596e-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="4596e-471">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-471">Default: `10`</span></span><br><span data-ttu-id="4596e-472">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-472">Min: `0`</span></span><br><span data-ttu-id="4596e-473">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="4596e-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4596e-474">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-475">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="4596e-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4596e-476">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="4596e-477">Al hospedar *en proceso*: El proceso **no** se reinicia y **no** usa la configuración `rapidFailsPerMinute`.</span><span class="sxs-lookup"><span data-stu-id="4596e-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="4596e-478">Al hospedar *fuera del proceso*: El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar `rapidFailsPerMinute` un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="4596e-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4596e-479">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="4596e-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4596e-480">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="4596e-480">Default: `120`</span></span><br><span data-ttu-id="4596e-481">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-481">Min: `0`</span></span><br><span data-ttu-id="4596e-482">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="4596e-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4596e-483">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-484">Si es true, **stdout** y **stderr** en el proceso especificado en `processPath` se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4596e-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4596e-485">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-485">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-486">Especifica la ruta de acceso relativa o absoluta para la que se registran `stdout` y `stderr` desde el proceso especificado en `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4596e-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="4596e-487">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4596e-488">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="4596e-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4596e-489">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-490">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="4596e-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="4596e-491">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como `stdout_20180205194132_1934.log` en la carpeta `logs`, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="4596e-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="4596e-492">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="4596e-492">Setting environment variables</span></span>

<span data-ttu-id="4596e-493">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4596e-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4596e-494">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4596e-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="4596e-495">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="4596e-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="4596e-496">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="4596e-496">The following example sets two environment variables.</span></span> <span data-ttu-id="4596e-497">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="4596e-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4596e-498">Un desarrollador puede establecer temporalmente este valor en el archivo `web.config` con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4596e-499">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="4596e-500">Una alternativa a establecer directamente el entorno en `web.config` consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="4596e-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4596e-501">Este método establece el entorno en `web.config` cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="4596e-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="4596e-502">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="4596e-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="4596e-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="4596e-503">app_offline.htm</span></span>

<span data-ttu-id="4596e-504">Si un archivo con el nombre `app_offline.htm` se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="4596e-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4596e-505">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="4596e-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4596e-506">Mientras el archivo `app_offline.htm` existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4596e-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="4596e-507">Cuando se quita el archivo `app_offline.htm`, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="4596e-508">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="4596e-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4596e-509">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4596e-510">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="4596e-510">Start-up error page</span></span>

<span data-ttu-id="4596e-511">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="4596e-512">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="4596e-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-513">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="4596e-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-514">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="4596e-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="4596e-515">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4596e-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4596e-516">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4596e-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4596e-517">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="4596e-517">Log creation and redirection</span></span>

<span data-ttu-id="4596e-518">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4596e-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4596e-519">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="4596e-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-520">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).</span><span class="sxs-lookup"><span data-stu-id="4596e-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="4596e-521">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4596e-522">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4596e-523">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4596e-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4596e-524">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="4596e-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4596e-525">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4596e-526">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4596e-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4596e-527">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="4596e-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4596e-528">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente `stdout`) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="4596e-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="4596e-529">Si la ruta de acceso de `stdoutLogFile` finaliza con `stdout`, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="4596e-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="4596e-530">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="4596e-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="4596e-531">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="4596e-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="4596e-532">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4596e-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4596e-533">Confirme que la identidad del usuario del grupo de aplicaciones tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="4596e-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="4596e-534">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-535">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="4596e-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4596e-536">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4596e-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="4596e-537">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="4596e-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="4596e-538">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="4596e-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="4596e-539">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` en `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="4596e-540">Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="4596e-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="4596e-541">El módulo no crea automáticamente las carpetas de la ruta de acceso proporcionada al valor `<handlerSetting>` (`logs` en el ejemplo anterior), que deben existir previamente en la implementación.</span><span class="sxs-lookup"><span data-stu-id="4596e-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="4596e-542">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).</span><span class="sxs-lookup"><span data-stu-id="4596e-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="4596e-543">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="4596e-544">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="4596e-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="4596e-545">ERROR</span><span class="sxs-lookup"><span data-stu-id="4596e-545">ERROR</span></span>
* <span data-ttu-id="4596e-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="4596e-546">WARNING</span></span>
* <span data-ttu-id="4596e-547">INFO</span><span class="sxs-lookup"><span data-stu-id="4596e-547">INFO</span></span>
* <span data-ttu-id="4596e-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="4596e-548">TRACE</span></span>

<span data-ttu-id="4596e-549">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="4596e-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="4596e-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="4596e-550">CONSOLE</span></span>
* <span data-ttu-id="4596e-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="4596e-551">EVENTLOG</span></span>
* <span data-ttu-id="4596e-552">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="4596e-552">FILE</span></span>

<span data-ttu-id="4596e-553">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="4596e-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="4596e-554">`ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="4596e-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="4596e-555">(Valor predeterminado: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="4596e-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="4596e-556">`ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="4596e-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="4596e-557">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="4596e-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="4596e-558">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="4596e-558">The size of the log isn't limited.</span></span> <span data-ttu-id="4596e-559">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="4596e-560">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4596e-561">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="4596e-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4596e-562">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="4596e-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="4596e-563">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4596e-564">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4596e-565">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="4596e-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4596e-566">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4596e-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4596e-567">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="4596e-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4596e-568">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="4596e-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4596e-569">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="4596e-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4596e-570">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4596e-571">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4596e-572">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="4596e-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4596e-573">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta `TrustedInstaller`.</span><span class="sxs-lookup"><span data-stu-id="4596e-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="4596e-574">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo `applicationHost.config` del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="4596e-575">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="4596e-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="4596e-576">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="4596e-577">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4596e-578">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="4596e-578">Run the installer.</span></span>
1. <span data-ttu-id="4596e-579">Exporte el archivo `applicationHost.config` actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="4596e-580">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4596e-581">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4596e-582">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4596e-583">En el sistema de hospedaje, vaya a `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="4596e-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="4596e-584">Busque el archivo `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="4596e-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="4596e-585">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="4596e-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4596e-586">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="4596e-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4596e-587">Los registros del instalador del conjunto de hospedaje para el módulo se encuentran en `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span><span class="sxs-lookup"><span data-stu-id="4596e-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="4596e-588">El archivo se denomina `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, donde el marcador de posición `{TIMESTAMP}` es la marca de tiempo.</span><span class="sxs-lookup"><span data-stu-id="4596e-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4596e-589">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4596e-590">Module</span><span class="sxs-lookup"><span data-stu-id="4596e-590">Module</span></span>

<span data-ttu-id="4596e-591">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="4596e-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="4596e-592">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="4596e-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="4596e-593">Schema</span><span class="sxs-lookup"><span data-stu-id="4596e-593">Schema</span></span>

<span data-ttu-id="4596e-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="4596e-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="4596e-596">Configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-596">Configuration</span></span>

<span data-ttu-id="4596e-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="4596e-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-598">**IIS Express**</span></span>

* <span data-ttu-id="4596e-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="4596e-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="4596e-600">CLI de *iisexpress.exe*: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="4596e-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="4596e-601">Los archivos se pueden encontrar mediante la búsqueda de `aspnetcore` en el archivo `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="4596e-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4596e-602">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para reenviar solicitudes web a aplicaciones ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="4596e-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="4596e-603">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="4596e-603">Supported Windows versions:</span></span>

* <span data-ttu-id="4596e-604">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="4596e-604">Windows 7 or later</span></span>
* <span data-ttu-id="4596e-605">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="4596e-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4596e-606">El módulo funciona únicamente con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4596e-606">The module only works with Kestrel.</span></span> <span data-ttu-id="4596e-607">El módulo no es compatible con [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4596e-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="4596e-608">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo también se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="4596e-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="4596e-609">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud, y reinicia la aplicación si esta se bloquea.</span><span class="sxs-lookup"><span data-stu-id="4596e-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="4596e-610">Este comportamiento es básicamente el mismo que el de las aplicaciones ASP.NET 4.x que se ejecutan en el proceso en IIS y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="4596e-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4596e-611">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación:</span><span class="sxs-lookup"><span data-stu-id="4596e-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="4596e-613">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="4596e-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4596e-614">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4596e-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4596e-615">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="4596e-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4596e-616">El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="4596e-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="4596e-617">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="4596e-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4596e-618">El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4596e-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4596e-619">Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4596e-620">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4596e-621">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4596e-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4596e-622">La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4596e-623">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="4596e-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4596e-624">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4596e-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4596e-625">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="4596e-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4596e-626">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="4596e-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4596e-627">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="4596e-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4596e-628">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4596e-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4596e-629">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4596e-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4596e-630">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4596e-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4596e-631">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="4596e-631">Configuration with web.config</span></span>

<span data-ttu-id="4596e-632">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4596e-633">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="4596e-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="4596e-634">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="4596e-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="4596e-635">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-636">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="4596e-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4596e-637">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4596e-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4596e-638">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4596e-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4596e-639">Atributo</span><span class="sxs-lookup"><span data-stu-id="4596e-639">Attribute</span></span> | <span data-ttu-id="4596e-640">Descripción</span><span class="sxs-lookup"><span data-stu-id="4596e-640">Description</span></span> | <span data-ttu-id="4596e-641">Default</span><span class="sxs-lookup"><span data-stu-id="4596e-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4596e-642">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-642">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-643">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4596e-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4596e-644">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-645">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="4596e-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4596e-646">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-647">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="4596e-648">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="4596e-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="4596e-649">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-650">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4596e-651">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="4596e-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4596e-652">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="4596e-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4596e-653">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-653">Default: `1`</span></span><br><span data-ttu-id="4596e-654">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="4596e-654">Min: `1`</span></span><br><span data-ttu-id="4596e-655">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="4596e-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="4596e-656">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="4596e-656">Required string attribute.</span></span></p><p><span data-ttu-id="4596e-657">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4596e-658">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="4596e-658">Relative paths are supported.</span></span> <span data-ttu-id="4596e-659">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4596e-660">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-661">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="4596e-662">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="4596e-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="4596e-663">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-663">Default: `10`</span></span><br><span data-ttu-id="4596e-664">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-664">Min: `0`</span></span><br><span data-ttu-id="4596e-665">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="4596e-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4596e-666">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4596e-667">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="4596e-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4596e-668">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="4596e-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="4596e-669">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-669">Default: `00:02:00`</span></span><br><span data-ttu-id="4596e-670">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-670">Min: `00:00:00`</span></span><br><span data-ttu-id="4596e-671">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4596e-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4596e-672">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-673">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4596e-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="4596e-674">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="4596e-674">Default: `10`</span></span><br><span data-ttu-id="4596e-675">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-675">Min: `0`</span></span><br><span data-ttu-id="4596e-676">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="4596e-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4596e-677">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="4596e-678">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="4596e-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4596e-679">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="4596e-680">El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="4596e-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4596e-681">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="4596e-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4596e-682">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="4596e-682">Default: `120`</span></span><br><span data-ttu-id="4596e-683">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="4596e-683">Min: `0`</span></span><br><span data-ttu-id="4596e-684">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="4596e-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4596e-685">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4596e-686">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4596e-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4596e-687">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="4596e-687">Optional string attribute.</span></span></p><p><span data-ttu-id="4596e-688">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4596e-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="4596e-689">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="4596e-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4596e-690">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="4596e-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4596e-691">Las carpetas que se proporcionan en la ruta de acceso deben estar en orden para que el módulo cree el archivo de registro.</span><span class="sxs-lookup"><span data-stu-id="4596e-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="4596e-692">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4596e-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="4596e-693">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="4596e-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="4596e-694">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="4596e-694">Setting environment variables</span></span>

<span data-ttu-id="4596e-695">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4596e-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4596e-696">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4596e-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="4596e-697">Las variables de entorno establecidas en esta sección entran en conflicto con las variables de entorno del sistema que tienen el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="4596e-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="4596e-698">Si se establece una variable de entorno en el archivo *web.config* y en el nivel del sistema en Windows, el valor del archivo *web.config* se anexa al valor de la variable de entorno del sistema (por ejemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), que impide que se inicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="4596e-699">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="4596e-699">The following example sets two environment variables.</span></span> <span data-ttu-id="4596e-700">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="4596e-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4596e-701">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4596e-702">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="4596e-703">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="4596e-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="4596e-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="4596e-704">app_offline.htm</span></span>

<span data-ttu-id="4596e-705">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="4596e-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4596e-706">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="4596e-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4596e-707">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4596e-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="4596e-708">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4596e-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4596e-709">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="4596e-709">Start-up error page</span></span>

<span data-ttu-id="4596e-710">Si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparece una página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="4596e-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="4596e-711">Para suprimir esta página y volver a la página de código de estado 502 de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4596e-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4596e-712">Para más información sobre cómo configurar mensajes de error personalizados, consulte [Errores HTTP\<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4596e-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4596e-713">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="4596e-713">Log creation and redirection</span></span>

<span data-ttu-id="4596e-714">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4596e-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4596e-715">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="4596e-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4596e-716">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="4596e-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="4596e-717">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="4596e-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4596e-718">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4596e-719">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4596e-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4596e-720">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="4596e-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4596e-721">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="4596e-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4596e-722">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4596e-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4596e-723">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="4596e-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4596e-724">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="4596e-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="4596e-725">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="4596e-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="4596e-726">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4596e-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4596e-727">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="4596e-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="4596e-728">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4596e-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4596e-729">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="4596e-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4596e-730">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4596e-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="4596e-731">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4596e-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4596e-732">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="4596e-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4596e-733">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4596e-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4596e-734">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4596e-735">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="4596e-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4596e-736">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4596e-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4596e-737">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="4596e-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4596e-738">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="4596e-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4596e-739">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="4596e-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4596e-740">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="4596e-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4596e-741">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4596e-742">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="4596e-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4596e-743">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="4596e-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="4596e-744">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="4596e-745">Al usar una configuración compartida de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="4596e-746">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4596e-747">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="4596e-747">Run the installer.</span></span>
1. <span data-ttu-id="4596e-748">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="4596e-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="4596e-749">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="4596e-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4596e-750">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="4596e-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4596e-751">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="4596e-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4596e-752">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="4596e-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="4596e-753">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="4596e-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="4596e-754">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="4596e-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4596e-755">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="4596e-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4596e-756">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="4596e-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4596e-757">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4596e-758">Module</span><span class="sxs-lookup"><span data-stu-id="4596e-758">Module</span></span>

<span data-ttu-id="4596e-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4596e-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="4596e-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4596e-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="4596e-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4596e-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="4596e-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4596e-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="4596e-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4596e-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="4596e-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4596e-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="4596e-765">Schema</span><span class="sxs-lookup"><span data-stu-id="4596e-765">Schema</span></span>

<span data-ttu-id="4596e-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-766">**IIS**</span></span>

* <span data-ttu-id="4596e-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="4596e-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="4596e-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-768">**IIS Express**</span></span>

* <span data-ttu-id="4596e-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="4596e-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="4596e-770">Configuración</span><span class="sxs-lookup"><span data-stu-id="4596e-770">Configuration</span></span>

<span data-ttu-id="4596e-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="4596e-771">**IIS**</span></span>

* <span data-ttu-id="4596e-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="4596e-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="4596e-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4596e-773">**IIS Express**</span></span>

* <span data-ttu-id="4596e-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="4596e-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="4596e-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="4596e-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="4596e-776">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="4596e-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4596e-777">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4596e-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="4596e-778">[Origen de referencia del módulo ASP.NET Core (rama maestra)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): use la lista desplegable **Rama** para seleccionar una versión específica (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="4596e-778">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
