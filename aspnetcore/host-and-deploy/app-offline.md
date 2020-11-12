---
title: Archivo App Offline (app_offline.htm)
author: rick-anderson
description: Obtenga información sobre cómo funciona el archivo App Offline (`app_offline.htm`) con el módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431035"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="76957-103">Archivo App Offline (`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="76957-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="76957-104">El módulo de ASP.NET Core usa el archivo App Offline (`app_offline.htm`) para cerrar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="76957-105">Si se detecta un archivo con el nombre `app_offline.htm` en el directorio raíz de una aplicación, el módulo de ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="76957-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="76957-106">Si la aplicación se sigue ejecutando después del número de segundos definido en `shutdownTimeLimit`, el módulo de ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="76957-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="76957-107">Mientras el archivo `app_offline.htm` existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="76957-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="76957-108">Cuando se quita el archivo `app_offline.htm`, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="76957-109">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="76957-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="76957-110">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="76957-111">Archivos de implementación bloqueados</span><span class="sxs-lookup"><span data-stu-id="76957-111">Locked deployment files</span></span>

<span data-ttu-id="76957-112">Los archivos de la carpeta de implementación se bloquean cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="76957-113">Los archivos bloqueados no se pueden sobrescribir durante la implementación.</span><span class="sxs-lookup"><span data-stu-id="76957-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="76957-114">`app_offline.htm` es el mecanismo principal para liberar archivos bloqueados.</span><span class="sxs-lookup"><span data-stu-id="76957-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="76957-115">Web Deploy usa `app_offline.htm` para detener e iniciar la aplicación correctamente.</span><span class="sxs-lookup"><span data-stu-id="76957-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="76957-116">`app_offline.htm` puede usarse manualmente para iniciar y detener la aplicación (requiere PowerShell 5 o posterior):</span><span class="sxs-lookup"><span data-stu-id="76957-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

<span data-ttu-id="76957-117">En el anterior script de PowerShell:</span><span class="sxs-lookup"><span data-stu-id="76957-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="76957-118">El marcador de posición `{PATH TO APP}` es la ruta de acceso a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="76957-119">El comando `New-Item` detiene el grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="76957-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="76957-120">El comando `Remove-Item` inicia el grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="76957-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="76957-121">El desarrollador proporciona comandos entre el comando `New-Item` y el comando `Remove-Item` para implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76957-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="76957-122">Los archivos también se pueden desbloquear si se detiene manualmente el grupo de aplicaciones en el Administrador de IIS en el servidor.</span><span class="sxs-lookup"><span data-stu-id="76957-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="76957-123">No use el archivo `app_offine.htm` al usar el Administrador de IIS para detener y reiniciar el grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="76957-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
