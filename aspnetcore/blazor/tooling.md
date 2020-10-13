---
title: Herramientas para ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las herramientas disponibles para compilar aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: d1626fe753782d524bf75c398c11235c3110633a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762157"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Herramientas para ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** . Seleccione **Siguiente**.

1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** . Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** . Seleccione **Crear**.

   Para obtener más información sobre los dos modelos de hospedaje de Blazor ( *Blazor WebAssembly* y *Blazor Server* ), vea <xref:blazor/hosting-models>.

1. Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.

Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

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

   Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obtener más información sobre los dos modelos de hospedaje de Blazor ( *Blazor WebAssembly* y *Blazor Server* ), vea <xref:blazor/hosting-models>.

1. Abra la carpeta `WebApplication1` en Visual Studio Code.

1. El IDE solicita que agregue recursos para compilar y depurar el proyecto. Seleccione **Sí**.

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

   Para obtener más información sobre los dos modelos de hospedaje de Blazor ( *Blazor WebAssembly* y *Blazor Server* ), vea <xref:blazor/hosting-models>.

1. Confirme que la **Autenticación** esté establecida en **Sin autenticación**. Seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`. Seleccione **Crear**.

1. Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*. Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.

Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado. Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end
