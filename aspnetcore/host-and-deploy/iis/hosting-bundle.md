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
# <a name="the-net-core-hosting-bundle"></a>Conjunto de hospedaje de .NET Core

El conjunto de hospedaje de .NET Core es un instalador para el entorno de ejecución de .NET Core y el [módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module). El conjunto permite que las aplicaciones de ASP.NET Core se ejecuten con IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Instalación del conjunto de hospedaje de .NET Core

> [!IMPORTANT]
> Si el conjunto de hospedaje se instala antes que IIS, se debe reparar la instalación de dicho conjunto. Vuelva a ejecutar el instalador del conjunto de hospedaje después de instalar IIS.
>
> Si el conjunto de hospedaje se instala después de hacer lo propio con la versión de 64 bits (x64) de .NET Core, es posible que los SDK no estén disponibles ([No se ha detectado ningún SDK de .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Para resolver el problema, consulte <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

## <a name="direct-download-current-version"></a>Descarga directa (versión actual)

Descargue al instalador mediante el vínculo siguiente:

[Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>Requisito de Visual C++ Redistributable

En versiones anteriores de Windows, por ejemplo, Windows Server 2012 R2, instale Visual Studio C++ 2015, 2017, 2019 Redistributable. De lo contrario, un mensaje de error confuso en el registro de eventos de Windows informa de que `The data is the error.`

[VS C++ Redistributable actual para x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[VS C++ Redistributable actual para x86](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>Versiones anteriores del instalador

Para obtener una versión anterior del instalador:

1. Vaya a la página [Descarga de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
1. Seleccione la versión de .NET Core que quiera.
1. En la columna **Run apps - Runtime** (Ejecutar aplicaciones - Runtime), busque la fila de la versión del runtime de .NET Core que quiera instalar.
1. Descargue el instalador mediante el vínculo del **conjunto de hospedaje**.

> [!WARNING]
> Algunos instaladores contienen versiones que han alcanzado el final del ciclo de vida (EOL) y ya no son compatibles con Microsoft. Para obtener más información, consulte la [política de soporte técnico](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="options"></a>Opciones

1. Los parámetros siguientes están disponibles cuando se ejecuta el instalador desde un shell de comandos de administrador:

   * `OPT_NO_ANCM=1`: omite la instalación del módulo de ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: omite la instalación del entorno de ejecución de .NET Core. Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: omite la instalación del marco compartido de ASP.NET (entorno de ejecución de ASP.NET). Se usa si el servidor solo hospeda [implementaciones autocontenidas (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: omite la instalación de entornos de ejecución x86. Utilice este parámetro cuando sepa que no va a hospedar aplicaciones de 32 bits. Si hay alguna posibilidad de que vaya a hospedar aplicaciones de 32 bits y 64 bits en el futuro, no use este parámetro e instale ambos entornos de ejecución.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: deshabilita la comprobación para usar una configuración compartida de IIS cuando la configuración compartida (`applicationHost.config`) está en la misma máquina que la instalación de IIS. *Solo disponible para ASP.NET Core 2.2 o instaladores del conjunto de hospedaje posteriores.* Para obtener más información, vea <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.

> [!NOTE]
> Para obtener información sobre la configuración compartida de IIS, vea [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration) (Módulo de ASP.NET Core con configuración compartida de IIS).

## <a name="restart-iis"></a>Reiniciar IIS

Una vez instalado el conjunto de hospedaje, es posible que se requiera un reinicio manual de IIS. Por ejemplo, puede que las herramientas de la CLI de `dotnet` (comando) no existan en la ruta de acceso para ejecutar procesos de trabajo de IIS.

Para detener e iniciar IIS manualmente, ejecute los siguientes comandos en un shell de comandos con privilegios elevados:

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versión del módulo y registros del instalador de la agrupación de hospedaje

Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:

1. En el sistema de hospedaje, vaya a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.
1. Busque el archivo `aspnetcorev2.dll`.
1. Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.
1. Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.

Los registros del instalador del conjunto de hospedaje para el módulo se encuentran en `C:\Users\%UserName%\AppData\Local\Temp`. El archivo se denomina `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, donde el marcador de posición `{TIMESTAMP}` es la marca de tiempo del archivo.
