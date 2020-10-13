---
title: Conjunto de hospedaje
author: rick-anderson
description: Aprenda a configurar el conjunto de hospedaje de .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755206"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="e065b-103">Conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e065b-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e065b-104">El conjunto de hospedaje de .NET Core es un instalador para el entorno de ejecución de .NET Core y el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e065b-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e065b-105">El conjunto permite que las aplicaciones de ASP.NET Core se ejecuten con IIS.</span><span class="sxs-lookup"><span data-stu-id="e065b-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e065b-106">Instalación del conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e065b-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e065b-107">Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto.</span><span class="sxs-lookup"><span data-stu-id="e065b-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e065b-108">Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.</span><span class="sxs-lookup"><span data-stu-id="e065b-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e065b-109">Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="e065b-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e065b-110">Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="e065b-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="e065b-111">Descarga directa (versión actual)</span><span class="sxs-lookup"><span data-stu-id="e065b-111">Direct download (current version)</span></span>

<span data-ttu-id="e065b-112">Descargue al instalador mediante el vínculo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e065b-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="e065b-113">Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)</span><span class="sxs-lookup"><span data-stu-id="e065b-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="e065b-114">Versiones anteriores del instalador</span><span class="sxs-lookup"><span data-stu-id="e065b-114">Earlier versions of the installer</span></span>

<span data-ttu-id="e065b-115">Para obtener una versión anterior del instalador:</span><span class="sxs-lookup"><span data-stu-id="e065b-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="e065b-116">Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="e065b-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e065b-117">Seleccione la versión de .NET Core que quiera.</span><span class="sxs-lookup"><span data-stu-id="e065b-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e065b-118">En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.</span><span class="sxs-lookup"><span data-stu-id="e065b-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e065b-119">Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.</span><span class="sxs-lookup"><span data-stu-id="e065b-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e065b-120">Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e065b-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e065b-121">Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="e065b-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e065b-122">Instalación del conjunto de hospedaje</span><span class="sxs-lookup"><span data-stu-id="e065b-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e065b-123">Ejecute el instalador en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e065b-123">Run the installer on the server.</span></span> <span data-ttu-id="e065b-124">Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:</span><span class="sxs-lookup"><span data-stu-id="e065b-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e065b-125">`OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e065b-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e065b-126">`OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e065b-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e065b-127">Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e065b-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e065b-128">`OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="e065b-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e065b-129">Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e065b-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e065b-130">`OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86.</span><span class="sxs-lookup"><span data-stu-id="e065b-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="e065b-131">Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="e065b-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e065b-132">Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.</span><span class="sxs-lookup"><span data-stu-id="e065b-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e065b-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (`applicationHost.config`) está en la misma máquina que la instalación de IIS.</span><span class="sxs-lookup"><span data-stu-id="e065b-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e065b-134">*Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.*</span><span class="sxs-lookup"><span data-stu-id="e065b-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e065b-135">Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e065b-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e065b-136">Reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e065b-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e065b-137">Al reiniciar IIS, se recoge un cambio en la variable PATH del sistema, que es una variable de entorno, realizado por el programa de instalación.</span><span class="sxs-lookup"><span data-stu-id="e065b-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e065b-138">ASP.NET Core no adopta el comportamiento de puesta al día para las versiones de revisión de los paquetes de marco compartidos.</span><span class="sxs-lookup"><span data-stu-id="e065b-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e065b-139">Después de actualizar el marco compartido mediante la instalación de un nuevo lote de hospedaje, reinicie el sistema o ejecute los comandos siguientes en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e065b-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="e065b-140">Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).</span><span class="sxs-lookup"><span data-stu-id="e065b-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="e065b-141">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="e065b-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="e065b-142">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="e065b-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="e065b-143">En el sistema de hospedaje, vaya a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="e065b-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="e065b-144">Busque el archivo `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="e065b-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="e065b-145">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="e065b-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="e065b-146">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="e065b-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="e065b-147">Los registros del instalador del conjunto de hospedaje para el módulo se encuentran en `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="e065b-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="e065b-148">El archivo se denomina `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, donde el marcador de posición `{TIMESTAMP}` es la marca de tiempo del archivo.</span><span class="sxs-lookup"><span data-stu-id="e065b-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
