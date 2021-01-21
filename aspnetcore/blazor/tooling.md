---
title: Herramientas para ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre las herramientas disponibles para compilar aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: 5901a1cb693dfe8e34e62ce2a28456bcf584221c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252271"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="67017-103">Herramientas para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="67017-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="67017-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="67017-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="67017-105">Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="67017-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="67017-106">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="67017-106">Create a new project.</span></span>

1. <span data-ttu-id="67017-107">Seleccione **Aplicación Blazor** .</span><span class="sxs-lookup"><span data-stu-id="67017-107">Select **Blazor App**.</span></span> <span data-ttu-id="67017-108">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="67017-108">Select **Next**.</span></span>

1. <span data-ttu-id="67017-109">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="67017-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="67017-110">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="67017-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="67017-111">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="67017-111">Select **Create**.</span></span>

1. <span data-ttu-id="67017-112">Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="67017-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="67017-113">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="67017-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="67017-114">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="67017-114">Select **Create**.</span></span>

   <span data-ttu-id="67017-115">En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="67017-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="67017-116">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="67017-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="67017-117">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67017-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="67017-118">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="67017-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="67017-119">Instale la última versión del [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="67017-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="67017-120">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="67017-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="67017-121">Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="67017-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="67017-122">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="67017-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="67017-123">Para disfrutar de una experiencia de Blazor WebAssembly, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="67017-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="67017-124">En el caso de una experiencia de Blazor WebAssembly hospedada, agregue la opción hospedada (`-ho` o `--hosted`) al comando:</span><span class="sxs-lookup"><span data-stu-id="67017-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="67017-125">Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="67017-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="67017-126">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="67017-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="67017-127">Abra la carpeta `WebApplication1` en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="67017-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="67017-128">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="67017-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="67017-129">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="67017-129">Select **Yes**.</span></span>

1. <span data-ttu-id="67017-130">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="67017-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="67017-131">Confianza en un certificado de desarrollo</span><span class="sxs-lookup"><span data-stu-id="67017-131">Trust a development certificate</span></span>

<span data-ttu-id="67017-132">No existe una manera centralizada de confiar en un certificado en Linux.</span><span class="sxs-lookup"><span data-stu-id="67017-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="67017-133">Normalmente, se adopta uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="67017-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="67017-134">Excluir la dirección URL de la aplicación en la lista de exclusión del explorador.</span><span class="sxs-lookup"><span data-stu-id="67017-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="67017-135">Confiar en todos los certificados autofirmados para `localhost`.</span><span class="sxs-lookup"><span data-stu-id="67017-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="67017-136">Agregar el certificado a la lista de certificados de confianza en el explorador.</span><span class="sxs-lookup"><span data-stu-id="67017-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="67017-137">Para más información, consulte las instrucciones proporcionadas por el fabricante de su explorador y la distribución de Linux.</span><span class="sxs-lookup"><span data-stu-id="67017-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="67017-138">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="67017-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="67017-139">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="67017-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="67017-140">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="67017-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="67017-141">Para disfrutar de una experiencia de Blazor WebAssembly, elija la plantilla **Aplicación Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="67017-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="67017-142">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="67017-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="67017-143">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="67017-143">Select **Next**.</span></span>

   <span data-ttu-id="67017-144">Para obtener información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* (independiente y hospedado) y *Blazor Server* , consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="67017-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="67017-145">Confirme que la **Autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="67017-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="67017-146">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="67017-146">Select **Next**.</span></span>

1. <span data-ttu-id="67017-147">En el caso de una experiencia de Blazor WebAssembly hospedada, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="67017-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="67017-148">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="67017-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="67017-149">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="67017-149">Select **Create**.</span></span>

1. <span data-ttu-id="67017-150">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="67017-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="67017-151">Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.</span><span class="sxs-lookup"><span data-stu-id="67017-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="67017-152">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="67017-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="67017-153">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="67017-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="67017-154">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="67017-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="67017-155">Uso de Visual Studio Code para el desarrollo multiplataforma de Blazor</span><span class="sxs-lookup"><span data-stu-id="67017-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="67017-156">[Visual Studio Code](https://code.visualstudio.com/) es un entorno de desarrollo integrado (IDE) multiplataforma de código abierto que se puede usar para desarrollar aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="67017-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="67017-157">Use la CLI de .NET para crear una aplicación Blazor para su desarrollo con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="67017-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="67017-158">Para obtener más información, consulte la versión [Linux de este artículo](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="67017-158">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="67017-159">Opciones de plantilla de Blazor</span><span class="sxs-lookup"><span data-stu-id="67017-159">Blazor template options</span></span>

<span data-ttu-id="67017-160">El marco de Blazor proporciona plantillas para la creación de aplicaciones para cada uno de los dos modelos de hospedaje de Blazor.</span><span class="sxs-lookup"><span data-stu-id="67017-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="67017-161">Las plantillas se usan para crear soluciones y proyectos de Blazor independientemente de las herramientas que seleccione para el desarrollo de Blazor (Visual Studio, Visual Studio para Mac, Visual Studio Code o la CLI de .NET):</span><span class="sxs-lookup"><span data-stu-id="67017-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="67017-162">Plantilla de proyecto de Blazor WebAssembly: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="67017-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="67017-163">Plantilla de proyecto de Blazor Server: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="67017-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="67017-164">Para más información sobre los modelos de hospedaje de Blazor, vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="67017-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="67017-165">Las opciones de plantilla están disponibles si se pasa la opción de ayuda (`-h` o `--help`) al comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la CLI en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="67017-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```