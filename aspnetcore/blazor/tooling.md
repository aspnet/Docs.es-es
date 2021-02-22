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
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="e8493-103">Herramientas para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e8493-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="e8493-104">Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="e8493-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="e8493-105">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="e8493-105">Create a new project.</span></span>

1. <span data-ttu-id="e8493-106">Seleccione **Aplicación Blazor** .</span><span class="sxs-lookup"><span data-stu-id="e8493-106">Select **Blazor App**.</span></span> <span data-ttu-id="e8493-107">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e8493-107">Select **Next**.</span></span>

1. <span data-ttu-id="e8493-108">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e8493-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e8493-109">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e8493-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e8493-110">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e8493-110">Select **Create**.</span></span>

1. <span data-ttu-id="e8493-111">Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="e8493-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="e8493-112">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="e8493-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="e8493-113">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e8493-113">Select **Create**.</span></span>

   <span data-ttu-id="e8493-114">En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="e8493-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="e8493-115">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e8493-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e8493-116">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e8493-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="e8493-117">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="e8493-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="e8493-118">Al ejecutar una aplicación hospedada Blazor WebAssembly, ejecute la aplicación desde el proyecto **`Server`** de la solución.</span><span class="sxs-lookup"><span data-stu-id="e8493-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="e8493-119">Instale la última versión del [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e8493-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e8493-120">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e8493-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="e8493-121">Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="e8493-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="e8493-122">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="e8493-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="e8493-123">Para disfrutar de una experiencia de Blazor WebAssembly, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e8493-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="e8493-124">En el caso de una experiencia de Blazor WebAssembly hospedada, agregue la opción hospedada (`-ho` o `--hosted`) al comando:</span><span class="sxs-lookup"><span data-stu-id="e8493-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="e8493-125">Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e8493-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="e8493-126">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e8493-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e8493-127">Abra la carpeta `WebApplication1` en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e8493-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="e8493-128">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e8493-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="e8493-129">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e8493-129">Select **Yes**.</span></span>

   <span data-ttu-id="e8493-130">**Configuración de inicio y tarea de aplicación Blazor WebAssembly hospedada**</span><span class="sxs-lookup"><span data-stu-id="e8493-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="e8493-131">En el caso de las soluciones hospedadas de Blazor WebAssembly, agregue (o mueva) la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` a la carpeta principal de la solución, que es la carpeta que contiene los nombres de carpeta de proyecto típicos de `Client`, `Server` y `Shared`.</span><span class="sxs-lookup"><span data-stu-id="e8493-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="e8493-132">Actualice o confirme que la configuración de los archivos `launch.json` y `tasks.json` ejecuta una aplicación Blazor WebAssembly hospedada desde el proyecto **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="e8493-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="e8493-133">**`.vscode/launch.json`** (configuración de `launch`):</span><span class="sxs-lookup"><span data-stu-id="e8493-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="e8493-134">En la configuración anterior para el directorio de trabajo actual (`cwd`), el marcador de posición `{SERVER APP FOLDER}` es la carpeta del proyecto **`Server`** , normalmente "`Server`".</span><span class="sxs-lookup"><span data-stu-id="e8493-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="e8493-135">Si se usa Microsoft Edge y Google Chrome no está instalado en el sistema, agregue una propiedad adicional de `"browser": "edge"` a la configuración.</span><span class="sxs-lookup"><span data-stu-id="e8493-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="e8493-136">Ejemplo de una carpeta de proyecto de `Server` que genera Microsoft Edge como explorador para las ejecuciones de depuración, en lugar del explorador predeterminado Google Chrome:</span><span class="sxs-lookup"><span data-stu-id="e8493-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="e8493-137">**`.vscode/tasks.json`** (argumentos del [comando `dotnet`](/dotnet/core/tools/dotnet)):</span><span class="sxs-lookup"><span data-stu-id="e8493-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="e8493-138">En el argumento anterior:</span><span class="sxs-lookup"><span data-stu-id="e8493-138">In the preceding argument:</span></span>

   * <span data-ttu-id="e8493-139">El marcador de posición `{SERVER APP FOLDER}` es la carpeta del proyecto **`Server`** , normalmente "`Server`".</span><span class="sxs-lookup"><span data-stu-id="e8493-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="e8493-140">El marcador de posición `{PROJECT NAME}` es el nombre de la aplicación, normalmente basado en el nombre de la solución seguido de "`.Server`" en una aplicación generada a partir de la plantilla de proyecto Blazor.</span><span class="sxs-lookup"><span data-stu-id="e8493-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the Blazor project template.</span></span>

   <span data-ttu-id="e8493-141">En el ejemplo siguiente del [tutorial para usar SignalR con una aplicación Blazor WebAssembly](xref:tutorials/signalr-blazor), se usa un nombre de carpeta de proyecto `Server` y un nombre de proyecto `BlazorWebAssemblySignalRApp.Server`:</span><span class="sxs-lookup"><span data-stu-id="e8493-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

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

1. <span data-ttu-id="e8493-142">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e8493-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="e8493-143">Confianza en un certificado de desarrollo</span><span class="sxs-lookup"><span data-stu-id="e8493-143">Trust a development certificate</span></span>

<span data-ttu-id="e8493-144">No existe una manera centralizada de confiar en un certificado en Linux.</span><span class="sxs-lookup"><span data-stu-id="e8493-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="e8493-145">Normalmente, se adopta uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="e8493-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="e8493-146">Excluir la dirección URL de la aplicación en la lista de exclusión del explorador.</span><span class="sxs-lookup"><span data-stu-id="e8493-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="e8493-147">Confiar en todos los certificados autofirmados para `localhost`.</span><span class="sxs-lookup"><span data-stu-id="e8493-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="e8493-148">Agregar el certificado a la lista de certificados de confianza en el explorador.</span><span class="sxs-lookup"><span data-stu-id="e8493-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="e8493-149">Para más información, consulte las instrucciones proporcionadas por el fabricante de su explorador y la distribución de Linux.</span><span class="sxs-lookup"><span data-stu-id="e8493-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="e8493-150">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="e8493-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="e8493-151">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="e8493-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="e8493-152">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="e8493-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="e8493-153">Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="e8493-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="e8493-154">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="e8493-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="e8493-155">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e8493-155">Select **Next**.</span></span>

   <span data-ttu-id="e8493-156">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e8493-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e8493-157">Confirme que la **Autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="e8493-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="e8493-158">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e8493-158">Select **Next**.</span></span>

1. <span data-ttu-id="e8493-159">En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="e8493-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="e8493-160">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="e8493-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="e8493-161">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e8493-161">Select **Create**.</span></span>

1. <span data-ttu-id="e8493-162">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="e8493-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="e8493-163">Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.</span><span class="sxs-lookup"><span data-stu-id="e8493-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="e8493-164">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="e8493-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="e8493-165">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="e8493-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="e8493-166">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="e8493-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="e8493-167">Al ejecutar una aplicación hospedada Blazor WebAssembly, ejecute la aplicación desde el proyecto **`Server`** de la solución.</span><span class="sxs-lookup"><span data-stu-id="e8493-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="e8493-168">Uso de Visual Studio Code para el desarrollo multiplataforma de Blazor</span><span class="sxs-lookup"><span data-stu-id="e8493-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="e8493-169">[Visual Studio Code](https://code.visualstudio.com/) es un entorno de desarrollo integrado (IDE) multiplataforma de código abierto que se puede usar para desarrollar aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="e8493-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="e8493-170">Use la CLI de .NET para crear una aplicación Blazor para su desarrollo con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e8493-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="e8493-171">Para obtener más información, consulte la versión [Linux de este artículo](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="e8493-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="e8493-172">Opciones de plantilla de Blazor</span><span class="sxs-lookup"><span data-stu-id="e8493-172">Blazor template options</span></span>

<span data-ttu-id="e8493-173">El marco de Blazor proporciona plantillas para la creación de aplicaciones para cada uno de los dos modelos de hospedaje de Blazor.</span><span class="sxs-lookup"><span data-stu-id="e8493-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="e8493-174">Las plantillas se usan para crear soluciones y proyectos de Blazor independientemente de las herramientas que seleccione para el desarrollo de Blazor (Visual Studio, Visual Studio para Mac, Visual Studio Code o la CLI de .NET):</span><span class="sxs-lookup"><span data-stu-id="e8493-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="e8493-175">Plantilla de proyecto de Blazor WebAssembly: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="e8493-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="e8493-176">Plantilla de proyecto de Blazor Server: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="e8493-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="e8493-177">Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e8493-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="e8493-178">Las opciones de plantilla están disponibles si se pasa la opción de ayuda (`-h` o `--help`) al comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la CLI en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e8493-178">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
