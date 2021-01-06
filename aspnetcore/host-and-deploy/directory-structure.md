---
title: Estructura de directorios de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la estructura de directorios de las aplicaciones ASP.NET Core publicadas.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
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
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 918bc11e06b8f2bea5506d3b61f462e15998efa0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059863"
---
# <a name="aspnet-core-directory-structure"></a>Estructura de directorios de ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

El directorio *publish* contiene recursos de la aplicación producidos por el comando [dotnet publish](/dotnet/core/tools/dotnet-publish) que se pueden implementar. El directorio contiene lo siguiente:

* Archivos de aplicación
* Archivos de configuración
* Recursos estáticos
* Paquetes
* Entorno de ejecución (solo [implementación autocontenida](/dotnet/core/deploying/#self-contained-deployments-scd))

| Tipo de aplicación | Estructura de directorios |
| -------- | ------------------- |
| [Archivo ejecutable dependiente del marco de trabajo (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Views&dagger; Aplicaciones MVC; si las vistas no están precompiladas</li><li>Pages&dagger; Aplicaciones de MVC o de Razor Pages, si las páginas no están precompiladas</li><li>wwwroot&dagger;</li><li>archivos \*.dll</li><li>{NOMBRE DE ENSAMBLADO}.deps.json</li><li>{NOMBRE DE ENSAMBLADO}.dll</li><li>Extensión {NOMBRE DE ENSAMBLADO}{.EXTENSIÓN} .exe en Windows, no en macOS ni Linux</li><li>{NOMBRE DE ENSAMBLADO}.pdb</li><li>{NOMBRE DE ENSAMBLADO}.Views.dll</li><li>{NOMBRE DE ENSAMBLADO}.Views.pdb</li><li>{NOMBRE DE ENSAMBLADO}.runtimeconfig.json</li><li>web.config (implementaciones de IIS)</li><li>createdump ([utilidad createdump de Linux](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (biblioteca de objetos compartidos de Linux)</li><li>\*.a (archivo macOS)</li><li>\*.dylib (biblioteca dinámica de macOS)</li></ul></li></ul> |
| [Implementación autocontenida (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Views&dagger; Aplicaciones MVC; si las vistas no están precompiladas</li><li>Pages&dagger; Aplicaciones de MVC o de Razor Pages, si las páginas no están precompiladas</li><li>wwwroot&dagger;</li><li>archivos \*.dll</li><li>{NOMBRE DE ENSAMBLADO}.deps.json</li><li>{NOMBRE DE ENSAMBLADO}.dll</li><li>{NOMBRE DE ENSAMBLADO}.exe</li><li>{NOMBRE DE ENSAMBLADO}.pdb</li><li>{NOMBRE DE ENSAMBLADO}.Views.dll</li><li>{NOMBRE DE ENSAMBLADO}.Views.pdb</li><li>{NOMBRE DE ENSAMBLADO}.runtimeconfig.json</li><li>web.config (implementaciones de IIS)</li></ul></li></ul> |

&dagger;Indica un directorio

El directorio *publish* representa la *ruta de acceso raíz del contenido*, también conocida como la *ruta de acceso base de aplicación*, de la implementación. Sea cual sea el nombre que se asigna al directorio *publish* de la aplicación implementada en el servidor, su ubicación funciona como la ruta física del servidor a la aplicación hospedada.

El directorio *wwwroot*, si existe, solo contiene recursos estáticos.

## <a name="additional-resources"></a>Recursos adicionales

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* [Marcos de trabajo de destino](/dotnet/standard/frameworks)
* [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El directorio *publish* contiene recursos de la aplicación producidos por el comando [dotnet publish](/dotnet/core/tools/dotnet-publish) que se pueden implementar. El directorio contiene lo siguiente:

* Archivos de aplicación
* Archivos de configuración
* Recursos estáticos
* Paquetes
* Entorno de ejecución (solo [implementación autocontenida](/dotnet/core/deploying/#self-contained-deployments-scd))

| Tipo de aplicación | Estructura de directorios |
| -------- | ------------------- |
| [Archivo ejecutable dependiente del marco de trabajo (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Views&dagger; Aplicaciones MVC; si las vistas no están precompiladas</li><li>Pages&dagger; Aplicaciones de MVC o de Razor Pages, si las páginas no están precompiladas</li><li>wwwroot&dagger;</li><li>archivos \*.dll</li><li>{NOMBRE DE ENSAMBLADO}.deps.json</li><li>{NOMBRE DE ENSAMBLADO}.dll</li><li>Extensión {NOMBRE DE ENSAMBLADO}{.EXTENSIÓN} .exe en Windows, no en macOS ni Linux</li><li>{NOMBRE DE ENSAMBLADO}.pdb</li><li>{NOMBRE DE ENSAMBLADO}.Views.dll</li><li>{NOMBRE DE ENSAMBLADO}.Views.pdb</li><li>{NOMBRE DE ENSAMBLADO}.runtimeconfig.json</li><li>web.config (implementaciones de IIS)</li><li>createdump ([utilidad createdump de Linux](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (biblioteca de objetos compartidos de Linux)</li><li>\*.a (archivo macOS)</li><li>\*.dylib (biblioteca dinámica de macOS)</li></ul></li></ul> |
| [Implementación autocontenida (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Views&dagger; Aplicaciones MVC; si las vistas no están precompiladas</li><li>Pages&dagger; Aplicaciones de MVC o de Razor Pages, si las páginas no están precompiladas</li><li>wwwroot&dagger;</li><li>archivos \*.dll</li><li>{NOMBRE DE ENSAMBLADO}.deps.json</li><li>{NOMBRE DE ENSAMBLADO}.dll</li><li>{NOMBRE DE ENSAMBLADO}.exe</li><li>{NOMBRE DE ENSAMBLADO}.pdb</li><li>{NOMBRE DE ENSAMBLADO}.Views.dll</li><li>{NOMBRE DE ENSAMBLADO}.Views.pdb</li><li>{NOMBRE DE ENSAMBLADO}.runtimeconfig.json</li><li>web.config (implementaciones de IIS)</li></ul></li></ul> |

&dagger;Indica un directorio

El directorio *publish* representa la *ruta de acceso raíz del contenido*, también conocida como la *ruta de acceso base de aplicación*, de la implementación. Sea cual sea el nombre que se asigna al directorio *publish* de la aplicación implementada en el servidor, su ubicación funciona como la ruta física del servidor a la aplicación hospedada.

El directorio *wwwroot*, si existe, solo contiene recursos estáticos.

Crear una carpeta *Logs* es útil para el [registro de depuración mejorado del módulo de ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). El módulo no crea automáticamente las carpetas de la ruta de acceso proporcionada al valor `<handlerSetting>`, que deben existir previamente en la implementación para permitir que el módulo escriba el registro de depuración.

Se puede crear un directorio *Logs* para la implementación mediante uno de los dos enfoques siguientes:

* Agregue el siguiente elemento `<Target>` al archivo del proyecto:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   El elemento `<MakeDir>` crea una carpeta *Logs* vacía en la salida publicada. El elemento usa la propiedad `PublishDir` para determinar la ubicación de destino para la creación de la carpeta. Varios métodos de implementación, como Web Deploy, omiten las carpetas vacías durante la implementación. El elemento `<WriteLinesToFile>` genera un archivo en la carpeta *Logs*, que garantiza la implementación de la carpeta en el servidor. Puede producirse un error en la creación de carpetas si el proceso de trabajo no tiene acceso de escritura a la carpeta de destino.

* Cree físicamente el directorio *Logs* en el servidor de la implementación.

El directorio de implementación requiere permisos de lectura y ejecución. El directorio *Logs* requiere permisos de lectura y escritura. Otros directorios donde se escriben los archivos requieren permisos de lectura y escritura.

## <a name="additional-resources"></a>Recursos adicionales

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* [Marcos de trabajo de destino](/dotnet/standard/frameworks)
* [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog)

::: moniker-end
