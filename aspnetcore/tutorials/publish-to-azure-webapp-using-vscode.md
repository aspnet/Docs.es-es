---
title: Publicación de una aplicación de ASP.NET Core en Azure con Visual Studio Code
author: rick-anderson
description: Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio Code.
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061267"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publicación de una aplicación de ASP.NET Core en Azure con Visual Studio Code

Autor: [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Para solucionar un problema de implementación de App Service, vea <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Introducción

En este tutorial aprenderá a crear una aplicación MVC ASP.Net Core y a implementarla en Visual Studio Code.

## <a name="set-up"></a>Configurar

- Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una.
- Instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).
- Instalación de [Visual Studio Code](https://code.visualstudio.com/Download)
  - Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en Visual Studio Code.
  - Instale la [extensión de Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) en Visual Studio Code y configúrela antes de continuar.

## <a name="create-an-aspnet-core-mvc-project"></a>Creación de un proyecto MVC ASP.Net Core

Mediante un terminal, navegue a la carpeta donde quiera crear el proyecto y use el comando siguiente:

```dotnetcli
dotnet new mvc
```

Tendrá una estructura de carpetas similar a la siguiente:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Apertura con Visual Studio Code

Una vez que haya creado el proyecto, podrá abrirlo con Visual Studio Code con una de las siguientes opciones:

### <a name="through-the-command-line"></a>Mediante la línea de comandos

Use el comando siguiente en la carpeta en la que ha creado el proyecto:

```cmd
> code .
```

Si el comando siguiente no funciona, compruebe si la instalación está configurada adecuadamente de acuerdo al contenido de [este vínculo](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Mediante la interfaz de Visual Studio Code

- Abra Visual Studio Code.
- En el menú, seleccione `File > Open Folder`.
- Seleccione la raíz de la carpeta en la que ha creado el proyecto MVC.

Al abrir la carpeta del proyecto, recibirá un mensaje en el que se indica que faltan los recursos requeridos para realizar la compilación y la depuración. Acepte la ayuda para agregarlos.

![Interfaz de Visual Studio Code con un proyecto cargado](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Se creará una carpeta `.vscode` en la estructura del proyecto. Contendrá los siguientes archivos:

```cmd
launch.json
tasks.json
```

Estos son archivos de utilidad que le ayudarán a compilar y depurar su aplicación web .NET Core.

## <a name="run-the-app"></a>Ejecutar la aplicación

Antes de implementar la aplicación en Azure, asegúrese de que se ejecuta correctamente en la máquina local.

- Presione F5 para ejecutar el proyecto.

La aplicación web se ejecutará en una pestaña nueva del explorador predeterminado. Es posible que se muestre una advertencia de privacidad en cuanto se inicie. Esto se debe a que la aplicación se iniciará con HTTP o HTTPS y navegará al punto de conexión HTTPS de manera predeterminada.

![Advertencia de privacidad al depurar la aplicación localmente](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Para mantener la sesión de depuración, haga clic en `Advanced` y luego en `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Generación del paquete de implementación localmente

- Abra un terminal de Visual Studio Code.
- Use el comando siguiente para generar un paquete `Release` en una subcarpeta denominada `publish`:
  - `dotnet publish -c Release -o ./publish`
- Se creará una nueva carpeta `publish` en la estructura del proyecto.

![Estructura de carpetas de publicación](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publicación en Azure App Service

Aproveche la extensión de Azure App Service para Visual Studio Code y siga los pasos que se indican a continuación para publicar el sitio web directamente en Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Si está creando una aplicación web

- Haga clic con el botón derecho en la carpeta `publish` y seleccione `Deploy to Web App...`.
- Seleccione la suscripción que quiera crear en la aplicación web.
- Seleccione `Create New Web App`.
- Escriba un nombre para la aplicación web.

La extensión creará la nueva aplicación web e iniciará automáticamente la implementación del paquete. Una vez que se haya terminado la implementación, haga clic en `Browse Website` para validar la implementación.

![Mensaje en el que se indica que la implementación se ha realizado correctamente](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Al hacer clic en `Browse Website`, navegará ahí con su explorador predeterminado:

![Nueva aplicación web implementada correctamente](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Si va a realizar la implementación en una aplicación web existente

- Haga clic con el botón derecho en la carpeta `publish` y seleccione `Deploy to Web App...`.
- Seleccione la suscripción en la que reside la aplicación web existente.
- Seleccione la aplicación web de la lista.
- Visual Studio Code le preguntará si quiere sobrescribir el contenido existente. Haga clic en `Deploy` para confirmar.

La extensión implementará el contenido actualizado en la aplicación web. Cuando termine, haga clic en `Browse Website` para validar la implementación.

![Aplicación web existente implementada correctamente.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Pasos siguientes

- [Create your first Azure DevOps pipeline](/azure/devops/pipelines/create-first-pipeline) (Creación de su primera canalización de Azure DevOps)

## <a name="additional-resources"></a>Recursos adicionales

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Grupos de recursos de Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
