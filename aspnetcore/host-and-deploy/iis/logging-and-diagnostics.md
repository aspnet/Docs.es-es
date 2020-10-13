---
title: Creación y redireccionamiento de registros con el módulo de ASP.NET Core
author: rick-anderson
description: Configure IIS y el módulo de ASP.NET Core para capturar registros e información de diagnóstico.
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 9af2311dd1f42cce3547c8215af22d2613453510
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755218"
---
# <a name="log-creation-and-redirection"></a>Creación y redireccionamiento de registros

El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`. Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`. El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).

Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso. Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.

Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.

No use el registro de stdout con fines de registro de aplicaciones general. Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros. Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).

Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo. El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( `.log`) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente `stdout`) delimitados por caracteres de subrayado. Si la ruta de acceso de `stdoutLogFile` finaliza con `stdout`, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo `stdout_20180205194132_1934.log`.

Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB. Después del inicio, se descartarán los registros adicionales.

En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`. Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`. La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.

Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.

Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Registros de diagnóstico mejorados

El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados. Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` en `web.config`. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:

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

Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (`logs` en el ejemplo anterior). El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\{APP POOL NAME}` para proporcionar permiso de escritura, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones).

Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.

Niveles (en orden de menos a más detallado):

* ERROR
* WARNING
* INFO
* TRACE

Ubicaciones (se permiten varias ubicaciones):

* CONSOLE
* EVENTLOG
* ARCHIVO

También se puede proporcionar la configuración de controlador a través de variables de entorno:

* `ASPNETCORE_MODULE_DEBUG_FILE`: ruta de acceso al archivo de registro de depuración. (Valor predeterminado: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`: valor de nivel de depuración.

> [!WARNING]
> **No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema. El tamaño del registro no es limitado. Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.

Consulte [Configuración con `web.config`](xref:host-and-deploy/iis/web-config#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo `web.config`.
