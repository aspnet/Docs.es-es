---
title: Creación y redireccionamiento de registros con el módulo de ASP.NET Core
author: rick-anderson
description: Configure IIS y el módulo de ASP.NET Core para capturar registros e información de diagnóstico.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057445"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="f3206-103">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="f3206-103">Log creation and redirection</span></span>

<span data-ttu-id="f3206-104">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f3206-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f3206-105">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="f3206-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f3206-106">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).</span><span class="sxs-lookup"><span data-stu-id="f3206-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f3206-107">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="f3206-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f3206-108">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="f3206-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f3206-109">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f3206-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f3206-110">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="f3206-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f3206-111">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="f3206-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f3206-112">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f3206-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f3206-113">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="f3206-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f3206-114">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente `stdout`) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="f3206-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="f3206-115">Si la ruta de acceso de `stdoutLogFile` finaliza con `stdout`, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="f3206-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="f3206-116">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="f3206-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f3206-117">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="f3206-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f3206-118">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="f3206-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f3206-119">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="f3206-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f3206-120">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f3206-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f3206-121">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f3206-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f3206-122">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3206-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f3206-123">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="f3206-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f3206-124">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="f3206-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f3206-125">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="f3206-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f3206-126">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` en `web.config`.</span><span class="sxs-lookup"><span data-stu-id="f3206-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="f3206-127">Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="f3206-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f3206-128">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (`logs` en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="f3206-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f3206-129">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).</span><span class="sxs-lookup"><span data-stu-id="f3206-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f3206-130">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="f3206-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f3206-131">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="f3206-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f3206-132">ERROR</span><span class="sxs-lookup"><span data-stu-id="f3206-132">ERROR</span></span>
* <span data-ttu-id="f3206-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="f3206-133">WARNING</span></span>
* <span data-ttu-id="f3206-134">INFO</span><span class="sxs-lookup"><span data-stu-id="f3206-134">INFO</span></span>
* <span data-ttu-id="f3206-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="f3206-135">TRACE</span></span>

<span data-ttu-id="f3206-136">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="f3206-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f3206-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f3206-137">CONSOLE</span></span>
* <span data-ttu-id="f3206-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f3206-138">EVENTLOG</span></span>
* <span data-ttu-id="f3206-139">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="f3206-139">FILE</span></span>

<span data-ttu-id="f3206-140">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="f3206-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f3206-141">`ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="f3206-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f3206-142">(Valor predeterminado: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="f3206-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="f3206-143">`ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="f3206-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f3206-144">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="f3206-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f3206-145">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="f3206-145">The size of the log isn't limited.</span></span> <span data-ttu-id="f3206-146">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="f3206-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f3206-147">Consulte [Configuración del módulo de ASP.NET Core con `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="f3206-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
