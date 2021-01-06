---
title: Conjunto de hospedaje
author: rick-anderson
description: Aprenda a configurar el conjunto de hospedaje de .NET Core.
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93343642"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="40108-103">Conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="40108-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="40108-104">El conjunto de hospedaje de .NET Core es un instalador para el entorno de ejecución de .NET Core y el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="40108-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40108-105">El conjunto permite que las aplicaciones de ASP.NET Core se ejecuten con IIS.</span><span class="sxs-lookup"><span data-stu-id="40108-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="40108-106">Instalación del conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="40108-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40108-107">Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto.</span><span class="sxs-lookup"><span data-stu-id="40108-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="40108-108">Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.</span><span class="sxs-lookup"><span data-stu-id="40108-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="40108-109">Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="40108-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="40108-110">Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="40108-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="40108-111">Descarga directa (versión actual)</span><span class="sxs-lookup"><span data-stu-id="40108-111">Direct download (current version)</span></span>

<span data-ttu-id="40108-112">Descargue al instalador mediante el vínculo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40108-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="40108-113">Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)</span><span class="sxs-lookup"><span data-stu-id="40108-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="40108-114">Requisito de Visual C++ Redistributable</span><span class="sxs-lookup"><span data-stu-id="40108-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="40108-115">En versiones anteriores de Windows, por ejemplo, Windows Server 2012 R2, instale Visual Studio C++ 2015, 2017, 2019 Redistributable.</span><span class="sxs-lookup"><span data-stu-id="40108-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="40108-116">De lo contrario, un mensaje de error confuso en el registro de eventos de Windows informa de que `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="40108-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="40108-117">[VS C++ Redistributable actual para x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[VS C++ Redistributable actual para x86](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="40108-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="40108-118">Versiones anteriores del instalador</span><span class="sxs-lookup"><span data-stu-id="40108-118">Earlier versions of the installer</span></span>

<span data-ttu-id="40108-119">Para obtener una versión anterior del instalador:</span><span class="sxs-lookup"><span data-stu-id="40108-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="40108-120">Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="40108-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="40108-121">Seleccione la versión de .NET Core que quiera.</span><span class="sxs-lookup"><span data-stu-id="40108-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="40108-122">En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.</span><span class="sxs-lookup"><span data-stu-id="40108-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="40108-123">Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.</span><span class="sxs-lookup"><span data-stu-id="40108-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="40108-124">Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="40108-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="40108-125">Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="40108-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="40108-126">Opciones</span><span class="sxs-lookup"><span data-stu-id="40108-126">Options</span></span>

1. <span data-ttu-id="40108-127">Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:</span><span class="sxs-lookup"><span data-stu-id="40108-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="40108-128">`OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40108-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="40108-129">`OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="40108-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="40108-130">Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="40108-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40108-131">`OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="40108-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="40108-132">Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="40108-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40108-133">`OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86.</span><span class="sxs-lookup"><span data-stu-id="40108-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="40108-134">Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="40108-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="40108-135">Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.</span><span class="sxs-lookup"><span data-stu-id="40108-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="40108-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (`applicationHost.config`) está en la misma máquina que la instalación de IIS.</span><span class="sxs-lookup"><span data-stu-id="40108-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="40108-137">*Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.*</span><span class="sxs-lookup"><span data-stu-id="40108-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="40108-138">Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="40108-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="40108-139">Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).</span><span class="sxs-lookup"><span data-stu-id="40108-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="40108-140">Reiniciar IIS</span><span class="sxs-lookup"><span data-stu-id="40108-140">Restart IIS</span></span>

<span data-ttu-id="40108-141">Una vez instalado el conjunto de hospedaje, es posible que se requiera un reinicio manual de IIS.</span><span class="sxs-lookup"><span data-stu-id="40108-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="40108-142">Por ejemplo, puede que las herramientas de la CLI de `dotnet` (comando) no existan en la ruta de acceso para ejecutar procesos de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="40108-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="40108-143">Para detener e iniciar IIS manualmente, ejecute los siguientes comandos en un shell de comandos con privilegios elevados:</span><span class="sxs-lookup"><span data-stu-id="40108-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="40108-144">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="40108-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="40108-145">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="40108-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="40108-146">En el sistema de hospedaje, vaya a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="40108-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="40108-147">Busque el archivo `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="40108-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="40108-148">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="40108-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="40108-149">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="40108-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="40108-150">Los registros del instalador del conjunto de hospedaje para el módulo se encuentran en `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="40108-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="40108-151">El archivo se denomina `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, donde el marcador de posición `{TIMESTAMP}` es la marca de tiempo del archivo.</span><span class="sxs-lookup"><span data-stu-id="40108-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
