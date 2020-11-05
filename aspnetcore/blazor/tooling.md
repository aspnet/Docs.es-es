---
title: 'Herramientas para ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Obtenga información sobre las herramientas disponibles para compilar aplicaciones de :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054819"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="adf38-103">Herramientas para ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="adf38-103">Tooling for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="adf38-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="adf38-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="adf38-105">Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="adf38-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="adf38-106">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="adf38-106">Create a new project.</span></span>

1. <span data-ttu-id="adf38-107">Seleccione **Aplicación :::no-loc(Blazor):::** .</span><span class="sxs-lookup"><span data-stu-id="adf38-107">Select **:::no-loc(Blazor)::: App**.</span></span> <span data-ttu-id="adf38-108">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="adf38-108">Select **Next**.</span></span>

1. <span data-ttu-id="adf38-109">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="adf38-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="adf38-110">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="adf38-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="adf38-111">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="adf38-111">Select **Create**.</span></span>

1. <span data-ttu-id="adf38-112">Para disfrutar de una experiencia de :::no-loc(Blazor WebAssembly):::, elija la plantilla **Aplicación :::no-loc(Blazor WebAssembly):::** .</span><span class="sxs-lookup"><span data-stu-id="adf38-112">For a :::no-loc(Blazor WebAssembly)::: experience, choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="adf38-113">Para disfrutar de una experiencia de :::no-loc(Blazor Server):::, elija la plantilla **Aplicación :::no-loc(Blazor Server):::** .</span><span class="sxs-lookup"><span data-stu-id="adf38-113">For a :::no-loc(Blazor Server)::: experience, choose the **:::no-loc(Blazor Server)::: App** template.</span></span> <span data-ttu-id="adf38-114">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="adf38-114">Select **Create**.</span></span>

   <span data-ttu-id="adf38-115">Para obtener más información sobre los dos modelos de hospedaje de :::no-loc(Blazor)::: ( *:::no-loc(Blazor WebAssembly):::* y *:::no-loc(Blazor Server):::* ), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="adf38-115">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="adf38-116">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="adf38-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="adf38-117">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="adf38-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="adf38-118">Instale la última versión del [SDK de .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="adf38-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="adf38-119">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="adf38-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="adf38-120">Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="adf38-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="adf38-121">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente.</span><span class="sxs-lookup"><span data-stu-id="adf38-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="adf38-122">Para disfrutar de una experiencia de :::no-loc(Blazor WebAssembly):::, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="adf38-122">For a :::no-loc(Blazor WebAssembly)::: experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="adf38-123">Para disfrutar de una experiencia de :::no-loc(Blazor Server):::, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="adf38-123">For a :::no-loc(Blazor Server)::: experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="adf38-124">Para obtener más información sobre los dos modelos de hospedaje de :::no-loc(Blazor)::: ( *:::no-loc(Blazor WebAssembly):::* y *:::no-loc(Blazor Server):::* ), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="adf38-124">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="adf38-125">Abra la carpeta `WebApplication1` en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="adf38-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="adf38-126">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="adf38-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="adf38-127">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="adf38-127">Select **Yes**.</span></span>

1. <span data-ttu-id="adf38-128">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="adf38-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="adf38-129">Confianza en un certificado de desarrollo</span><span class="sxs-lookup"><span data-stu-id="adf38-129">Trust a development certificate</span></span>

<span data-ttu-id="adf38-130">No existe una manera centralizada de confiar en un certificado en Linux.</span><span class="sxs-lookup"><span data-stu-id="adf38-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="adf38-131">Normalmente, se adopta uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="adf38-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="adf38-132">Excluir la dirección URL de la aplicación en la lista de exclusión del explorador.</span><span class="sxs-lookup"><span data-stu-id="adf38-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="adf38-133">Confiar en todos los certificados autofirmados para `localhost`.</span><span class="sxs-lookup"><span data-stu-id="adf38-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="adf38-134">Agregar el certificado a la lista de certificados de confianza en el explorador.</span><span class="sxs-lookup"><span data-stu-id="adf38-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="adf38-135">Para más información, consulte las instrucciones proporcionadas por el fabricante de su explorador y la distribución de Linux.</span><span class="sxs-lookup"><span data-stu-id="adf38-135">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="adf38-136">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="adf38-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="adf38-137">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="adf38-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="adf38-138">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="adf38-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="adf38-139">Para disfrutar de una experiencia de :::no-loc(Blazor WebAssembly):::, elija la plantilla **Aplicación :::no-loc(Blazor WebAssembly):::** .</span><span class="sxs-lookup"><span data-stu-id="adf38-139">For a :::no-loc(Blazor WebAssembly)::: experience, choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="adf38-140">Para disfrutar de una experiencia de :::no-loc(Blazor Server):::, elija la plantilla **Aplicación :::no-loc(Blazor Server):::** .</span><span class="sxs-lookup"><span data-stu-id="adf38-140">For a :::no-loc(Blazor Server)::: experience, choose the **:::no-loc(Blazor Server)::: App** template.</span></span> <span data-ttu-id="adf38-141">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="adf38-141">Select **Next**.</span></span>

   <span data-ttu-id="adf38-142">Para obtener más información sobre los dos modelos de hospedaje de :::no-loc(Blazor)::: ( *:::no-loc(Blazor WebAssembly):::* y *:::no-loc(Blazor Server):::* ), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="adf38-142">For information on the two :::no-loc(Blazor)::: hosting models, *:::no-loc(Blazor WebAssembly):::* and *:::no-loc(Blazor Server):::* , see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="adf38-143">Confirme que la **Autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="adf38-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="adf38-144">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="adf38-144">Select **Next**.</span></span>

1. <span data-ttu-id="adf38-145">En el campo **Nombre del proyecto** , asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="adf38-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="adf38-146">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="adf38-146">Select **Create**.</span></span>

1. <span data-ttu-id="adf38-147">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="adf38-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="adf38-148">Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.</span><span class="sxs-lookup"><span data-stu-id="adf38-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="adf38-149">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="adf38-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="adf38-150">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="adf38-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="adf38-151">Para obtener más información sobre la confianza en el certificado de desarrollo de HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="adf38-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
