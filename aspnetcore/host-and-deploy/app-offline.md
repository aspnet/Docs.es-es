---
title: Archivo App Offline (app_offline.htm)
author: rick-anderson
description: Obtenga información sobre cómo funciona el archivo App Offline (`app_offline.htm`) con el módulo de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431035"
---
# <a name="app-offline-file-app_offlinehtm"></a>Archivo App Offline (`app_offline.htm`)

El módulo de ASP.NET Core usa el archivo App Offline (`app_offline.htm`) para cerrar una aplicación.

Si se detecta un archivo con el nombre `app_offline.htm` en el directorio raíz de una aplicación, el módulo de ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes. Si la aplicación se sigue ejecutando después del número de segundos definido en `shutdownTimeLimit`, el módulo de ASP.NET Core termina el proceso en ejecución.

Mientras el archivo `app_offline.htm` existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo `app_offline.htm`. Cuando se quita el archivo `app_offline.htm`, la solicitud siguiente inicia la aplicación.

Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta. Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.

## <a name="locked-deployment-files"></a>Archivos de implementación bloqueados

Los archivos de la carpeta de implementación se bloquean cuando se ejecuta la aplicación. Los archivos bloqueados no se pueden sobrescribir durante la implementación.

`app_offline.htm` es el mecanismo principal para liberar archivos bloqueados. Web Deploy usa `app_offline.htm` para detener e iniciar la aplicación correctamente.

`app_offline.htm` puede usarse manualmente para iniciar y detener la aplicación (requiere PowerShell 5 o posterior):

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

En el anterior script de PowerShell:

* El marcador de posición `{PATH TO APP}` es la ruta de acceso a la aplicación.
* El comando `New-Item` detiene el grupo de aplicaciones.
* El comando `Remove-Item` inicia el grupo de aplicaciones.
* El desarrollador proporciona comandos entre el comando `New-Item` y el comando `Remove-Item` para implementar la aplicación.

Los archivos también se pueden desbloquear si se detiene manualmente el grupo de aplicaciones en el Administrador de IIS en el servidor. No use el archivo `app_offine.htm` al usar el Administrador de IIS para detener y reiniciar el grupo de aplicaciones.
