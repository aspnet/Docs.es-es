---
title: Publicación de una aplicación de ASP.NET Core en Azure con Visual Studio Code
author: rick-anderson
description: Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio Code.
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
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
ms.openlocfilehash: 28074d64e9d4d0ec35af06e8b6daae939e316b2b
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130215"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="5bd2c-103">Publicación de una aplicación de ASP.NET Core en Azure con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bd2c-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="5bd2c-104">Autor: [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="5bd2c-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="5bd2c-105">Para solucionar un problema de implementación de App Service, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="5bd2c-106">Introducción</span><span class="sxs-lookup"><span data-stu-id="5bd2c-106">Intro</span></span>

<span data-ttu-id="5bd2c-107">En este tutorial aprenderá a crear una aplicación MVC ASP.Net Core y a implementarla en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="5bd2c-108">Configurar</span><span class="sxs-lookup"><span data-stu-id="5bd2c-108">Set up</span></span>

- <span data-ttu-id="5bd2c-109">Abra una [cuenta gratuita de Azure](https://azure.microsoft.com/free/dotnet/) si no tiene una.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="5bd2c-110">Instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5bd2c-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="5bd2c-111">Instalación de [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="5bd2c-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="5bd2c-112">Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="5bd2c-113">Instale la [extensión de Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) en Visual Studio Code y configúrela antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="5bd2c-114">Creación de un proyecto MVC ASP.Net Core</span><span class="sxs-lookup"><span data-stu-id="5bd2c-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="5bd2c-115">Mediante un terminal, navegue a la carpeta donde quiera crear el proyecto y use el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="5bd2c-116">Tendrá una estructura de carpetas similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="5bd2c-117">Apertura con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bd2c-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="5bd2c-118">Una vez que haya creado el proyecto, podrá abrirlo con Visual Studio Code con una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="5bd2c-119">Mediante la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="5bd2c-119">Through the command line</span></span>

<span data-ttu-id="5bd2c-120">Use el comando siguiente en la carpeta en la que ha creado el proyecto:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="5bd2c-121">Si el comando siguiente no funciona, compruebe si la instalación está configurada adecuadamente de acuerdo al contenido de [este vínculo](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="5bd2c-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="5bd2c-122">Mediante la interfaz de Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bd2c-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="5bd2c-123">Abra Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="5bd2c-124">En el menú, seleccione `File > Open Folder`.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="5bd2c-125">Seleccione la raíz de la carpeta en la que ha creado el proyecto MVC.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="5bd2c-126">Al abrir la carpeta del proyecto, recibirá un mensaje en el que se indica que faltan los recursos requeridos para realizar la compilación y la depuración.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="5bd2c-127">Acepte la ayuda para agregarlos.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-127">Accept the help to add them.</span></span>

![Interfaz de Visual Studio Code con un proyecto cargado](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="5bd2c-129">Se creará una carpeta `.vscode` en la estructura del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="5bd2c-130">Contendrá los siguientes archivos:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="5bd2c-131">Estos son archivos de utilidad que le ayudarán a compilar y depurar su aplicación web .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5bd2c-132">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="5bd2c-132">Run the app</span></span>

<span data-ttu-id="5bd2c-133">Antes de implementar la aplicación en Azure, asegúrese de que se ejecuta correctamente en la máquina local.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="5bd2c-134">Presione F5 para ejecutar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-134">Press F5 to run the project</span></span>

<span data-ttu-id="5bd2c-135">La aplicación web se ejecutará en una pestaña nueva del explorador predeterminado.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="5bd2c-136">Es posible que se muestre una advertencia de privacidad en cuanto se inicie.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="5bd2c-137">Esto se debe a que la aplicación se iniciará con HTTP o HTTPS y navegará al punto de conexión HTTPS de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Advertencia de privacidad al depurar la aplicación localmente](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="5bd2c-139">Para mantener la sesión de depuración, haga clic en `Advanced` y luego en `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="5bd2c-140">Generación del paquete de implementación localmente</span><span class="sxs-lookup"><span data-stu-id="5bd2c-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="5bd2c-141">Abra un terminal de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="5bd2c-142">Use el comando siguiente para generar un paquete `Release` en una subcarpeta denominada `publish`:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="5bd2c-143">Se creará una nueva carpeta `publish` en la estructura del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-143">A new `publish` folder will be created under the project structure</span></span>

![Estructura de carpetas de publicación](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="5bd2c-145">Publicación en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5bd2c-145">Publish to Azure App Service</span></span>

<span data-ttu-id="5bd2c-146">Aproveche la extensión de Azure App Service para Visual Studio Code y siga los pasos que se indican a continuación para publicar el sitio web directamente en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="5bd2c-147">Si está creando una aplicación web</span><span class="sxs-lookup"><span data-stu-id="5bd2c-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="5bd2c-148">Haga clic con el botón derecho en la carpeta `publish` y seleccione `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5bd2c-149">Seleccione la suscripción que quiera crear en la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="5bd2c-150">Seleccione `Create New Web App`.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="5bd2c-151">Escriba un nombre para la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-151">Enter a name for the Web App</span></span>

<span data-ttu-id="5bd2c-152">La extensión creará la nueva aplicación web e iniciará automáticamente la implementación del paquete.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="5bd2c-153">Una vez que se haya terminado la implementación, haga clic en `Browse Website` para validar la implementación.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Mensaje en el que se indica que la implementación se ha realizado correctamente](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="5bd2c-155">Al hacer clic en `Browse Website`, navegará ahí con su explorador predeterminado:</span><span class="sxs-lookup"><span data-stu-id="5bd2c-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nueva aplicación web implementada correctamente](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="5bd2c-157">Si va a realizar la implementación en una aplicación web existente</span><span class="sxs-lookup"><span data-stu-id="5bd2c-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="5bd2c-158">Haga clic con el botón derecho en la carpeta `publish` y seleccione `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5bd2c-159">Seleccione la suscripción en la que reside la aplicación web existente.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="5bd2c-160">Seleccione la aplicación web de la lista.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-160">Select the Web App from the list</span></span>
- <span data-ttu-id="5bd2c-161">Visual Studio Code le preguntará si quiere sobrescribir el contenido existente.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="5bd2c-162">Haga clic en `Deploy` para confirmar.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="5bd2c-163">La extensión implementará el contenido actualizado en la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="5bd2c-164">Cuando termine, haga clic en `Browse Website` para validar la implementación.</span><span class="sxs-lookup"><span data-stu-id="5bd2c-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Aplicación web existente implementada correctamente.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="5bd2c-166">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="5bd2c-166">Next steps</span></span>

- <span data-ttu-id="5bd2c-167">[Create your first Azure DevOps pipeline](/azure/devops/pipelines/create-first-pipeline) (Creación de su primera canalización de Azure DevOps)</span><span class="sxs-lookup"><span data-stu-id="5bd2c-167">[Create your first Azure DevOps pipeline](/azure/devops/pipelines/create-first-pipeline)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bd2c-168">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5bd2c-168">Additional resources</span></span>

- [<span data-ttu-id="5bd2c-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5bd2c-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="5bd2c-170">Grupos de recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="5bd2c-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
