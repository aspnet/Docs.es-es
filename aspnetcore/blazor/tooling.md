---
title: Herramientas para ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las herramientas disponibles para compilar aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536251"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Herramientas para ASP.NET Core Blazor

::: zone pivot="windows"

1. Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** . Seleccione **Siguiente**.

1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** . Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** . Seleccione **Crear**.

   En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.

   Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

Al ejecutar una aplicación hospedada Blazor WebAssembly, ejecute la aplicación desde el proyecto **`Server`** de la solución.

::: zone-end

::: zone pivot="linux"

1. Instale la última versión del [SDK de .NET Core](https://dotnet.microsoft.com/download). Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet --version
   ```

1. Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com).

1. Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.

1. Para disfrutar de una experiencia de Blazor WebAssembly, ejecute el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   En el caso de una experiencia de Blazor WebAssembly hospedada, agregue la opción hospedada (`-ho` o `--hosted`) al comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.

1. Abra la carpeta `WebApplication1` en Visual Studio Code.

1. El IDE solicita que agregue recursos para compilar y depurar el proyecto. Seleccione **Sí**.

   **Configuración de inicio y tarea de aplicación Blazor WebAssembly hospedada**

   En el caso de las soluciones hospedadas de Blazor WebAssembly, agregue (o mueva) la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` a la carpeta principal de la solución, que es la carpeta que contiene los nombres de carpeta de proyecto típicos de `Client`, `Server` y `Shared`. Actualice o confirme que la configuración de los archivos `launch.json` y `tasks.json` ejecuta una aplicación Blazor WebAssembly hospedada desde el proyecto **`Server`** .

   **`.vscode/launch.json`** (configuración de `launch`):

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   En la configuración anterior para el directorio de trabajo actual (`cwd`), el marcador de posición `{SERVER APP FOLDER}` es la carpeta del proyecto **`Server`** , normalmente "`Server`".

   Si se usa Microsoft Edge y Google Chrome no está instalado en el sistema, agregue una propiedad adicional de `"browser": "edge"` a la configuración.

   Ejemplo de una carpeta de proyecto de `Server` que genera Microsoft Edge como explorador para las ejecuciones de depuración, en lugar del explorador predeterminado Google Chrome:

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`** (argumentos del [comando `dotnet`](/dotnet/core/tools/dotnet)):

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   En el argumento anterior:

   * El marcador de posición `{SERVER APP FOLDER}` es la carpeta del proyecto **`Server`** , normalmente "`Server`".
   * El marcador de posición `{PROJECT NAME}` es el nombre de la aplicación, normalmente basado en el nombre de la solución seguido de "`.Server`" en una aplicación generada a partir de la plantilla de proyecto Blazor.

   En el ejemplo siguiente del [tutorial para usar SignalR con una aplicación Blazor WebAssembly](xref:tutorials/signalr-blazor), se usa un nombre de carpeta de proyecto `Server` y un nombre de proyecto `BlazorWebAssemblySignalRApp.Server`:

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

## <a name="trust-a-development-certificate"></a>Confianza en un certificado de desarrollo

No existe una manera centralizada de confiar en un certificado en Linux. Normalmente, se adopta uno de los enfoques siguientes:

* Excluir la dirección URL de la aplicación en la lista de exclusión del explorador.
* Confiar en todos los certificados autofirmados para `localhost`.
* Agregar el certificado a la lista de certificados de confianza en el explorador.

Para más información, consulte las instrucciones proporcionadas por el fabricante de su explorador y la distribución de Linux.

::: zone-end

::: zone pivot="macos"

1. Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.

1. En la barra lateral, seleccione **Web y consola** > **Aplicación**.

   Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** . Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** . Seleccione **Siguiente**.

   Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.

1. Confirme que la **Autenticación** esté establecida en **Sin autenticación**. Seleccione **Siguiente**.

1. En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`. Seleccione **Crear**.

1. Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*. Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.

Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado. Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

Al ejecutar una aplicación hospedada Blazor WebAssembly, ejecute la aplicación desde el proyecto **`Server`** de la solución.

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>Uso de Visual Studio Code para el desarrollo multiplataforma de Blazor

[Visual Studio Code](https://code.visualstudio.com/) es un entorno de desarrollo integrado (IDE) multiplataforma de código abierto que se puede usar para desarrollar aplicaciones Blazor. Use la CLI de .NET para crear una aplicación Blazor para su desarrollo con Visual Studio Code. Para obtener más información, consulte la versión [Linux de este artículo](?pivots=linux).

## <a name="blazor-template-options"></a>Opciones de plantilla de Blazor

El marco de Blazor proporciona plantillas para la creación de aplicaciones para cada uno de los dos modelos de hospedaje de Blazor. Las plantillas se usan para crear soluciones y proyectos de Blazor independientemente de las herramientas que seleccione para el desarrollo de Blazor (Visual Studio, Visual Studio para Mac, Visual Studio Code o la CLI de .NET):

* Plantilla de proyecto de Blazor WebAssembly: `blazorwasm`
* Plantilla de proyecto de Blazor Server: `blazorserver`

Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.

Las opciones de plantilla están disponibles si se pasa la opción de ayuda (`-h` o `--help`) al comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la CLI en un shell de comandos:

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
