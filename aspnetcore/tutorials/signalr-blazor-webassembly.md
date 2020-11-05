---
title: 'Uso de ASP.NET Core :::no-loc(SignalR)::: con :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Cree una aplicación de chat que use ASP.NET Core :::no-loc(SignalR)::: con :::no-loc(Blazor WebAssembly):::.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052713"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="0d9b1-103">Uso de ASP.NET Core :::no-loc(SignalR)::: con :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="0d9b1-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0d9b1-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante :::no-loc(SignalR)::: con :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="0d9b1-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d9b1-107">Crear un proyecto de aplicación :::no-loc(Blazor WebAssembly)::: hospedado</span><span class="sxs-lookup"><span data-stu-id="0d9b1-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="0d9b1-108">Adición de la biblioteca cliente de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="0d9b1-109">Agregar un concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="0d9b1-110">Agregar servicios de :::no-loc(SignalR)::: y un punto de conexión para el concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="0d9b1-111">Agregar código de componente de :::no-loc(Razor)::: para chat</span><span class="sxs-lookup"><span data-stu-id="0d9b1-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="0d9b1-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0d9b1-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d9b1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d9b1-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="0d9b1-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="0d9b1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d9b1-115">Visual Studio</span></span>](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* <span data-ttu-id="0d9b1-116">[Versión 16.8 o posterior (en versión preliminar) de Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="0d9b1-116">[Visual Studio 2019 16.8 or later (in preview)](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d9b1-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d9b1-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d9b1-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d9b1-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [<span data-ttu-id="0d9b1-119">Visual Studio para Mac, versión 8.8 o posterior (en versión preliminar)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-119">Visual Studio for Mac version 8.8 or later (in preview)</span></span>](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0d9b1-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9b1-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="0d9b1-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d9b1-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d9b1-122">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="0d9b1-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d9b1-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d9b1-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d9b1-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d9b1-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0d9b1-125">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="0d9b1-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0d9b1-126">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9b1-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="0d9b1-127">Creación de un proyecto de aplicación :::no-loc(Blazor WebAssembly)::: hospedado</span><span class="sxs-lookup"><span data-stu-id="0d9b1-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="0d9b1-128">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d9b1-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d9b1-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0d9b1-130">Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0d9b1-131">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="0d9b1-132">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-132">Create a new project.</span></span>

1. <span data-ttu-id="0d9b1-133">Seleccione **Aplicación :::no-loc(Blazor):::** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="0d9b1-134">Escriba `:::no-loc(Blazor)::::::no-loc(SignalR):::App` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="0d9b1-135">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0d9b1-136">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-136">Select **Create**.</span></span>

1. <span data-ttu-id="0d9b1-137">Elija la plantilla **Aplicación de :::no-loc(Blazor WebAssembly):::** .</span><span class="sxs-lookup"><span data-stu-id="0d9b1-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="0d9b1-138">En **Avanzado** , active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0d9b1-139">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d9b1-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d9b1-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0d9b1-141">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="0d9b1-142">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0d9b1-143">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0d9b1-144">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d9b1-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d9b1-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0d9b1-146">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="0d9b1-147">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0d9b1-148">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="0d9b1-149">Elija la plantilla **Aplicación de :::no-loc(Blazor WebAssembly):::** .</span><span class="sxs-lookup"><span data-stu-id="0d9b1-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="0d9b1-150">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-150">Select **Next**.</span></span>

1. <span data-ttu-id="0d9b1-151">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="0d9b1-152">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="0d9b1-153">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-153">Select **Next**.</span></span>

1. <span data-ttu-id="0d9b1-154">En el campo **Nombre del proyecto** , asigne un nombre a la aplicación `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="0d9b1-155">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-155">Select **Create**.</span></span>

   <span data-ttu-id="0d9b1-156">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0d9b1-157">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="0d9b1-158">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="0d9b1-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0d9b1-159">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9b1-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0d9b1-160">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="0d9b1-161">Adición de la biblioteca cliente de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d9b1-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d9b1-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0d9b1-163">En el **Explorador de soluciones** , haga clic con el botón derecho en el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0d9b1-164">En el cuadro de diálogo **Administrar paquetes NuGet** , confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0d9b1-165">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="0d9b1-166">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="0d9b1-167">Si aparece el cuadro de diálogo **Vista previa de los cambios** , seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0d9b1-168">Si aparece el cuadro de diálogo **Aceptación de la licencia** , seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d9b1-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d9b1-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0d9b1-170">En **Terminal integrado** ( **Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d9b1-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d9b1-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0d9b1-172">En la barra lateral **Solución** , haga clic con el botón derecho en el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0d9b1-173">En el cuadro de diálogo **Administrar paquetes NuGet** , confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0d9b1-174">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="0d9b1-175">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="0d9b1-176">Si aparece el cuadro de diálogo **Aceptación de la licencia** , seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0d9b1-177">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9b1-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0d9b1-178">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="0d9b1-179">Agregar un concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="0d9b1-180">En el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="0d9b1-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="0d9b1-181">Adición de servicios y de un punto de conexión para el concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="0d9b1-182">En el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="0d9b1-183">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="0d9b1-184">Agregue servicios de middleware de compresión de respuesta y :::no-loc(SignalR)::: a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="0d9b1-185">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="0d9b1-186">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="0d9b1-187">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="0d9b1-188">Agregar código de componente de :::no-loc(Razor)::: para chat</span><span class="sxs-lookup"><span data-stu-id="0d9b1-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="0d9b1-189">En el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="0d9b1-190">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="0d9b1-191">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="0d9b1-191">Run the app</span></span>

1. <span data-ttu-id="0d9b1-192">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d9b1-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d9b1-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0d9b1-194">En el **Explorador de soluciones** , seleccione el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="0d9b1-195">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0d9b1-196">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0d9b1-197">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0d9b1-198">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-198">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de :::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0d9b1-200">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d9b1-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d9b1-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0d9b1-202">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="0d9b1-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="0d9b1-203">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0d9b1-204">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0d9b1-205">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0d9b1-206">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-206">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de :::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0d9b1-208">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d9b1-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0d9b1-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0d9b1-210">En la barra lateral **Solución** , seleccione el proyecto `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="0d9b1-211">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0d9b1-212">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0d9b1-213">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0d9b1-214">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-214">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de :::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0d9b1-216">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0d9b1-217">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9b1-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0d9b1-218">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0d9b1-219">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0d9b1-220">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="0d9b1-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0d9b1-221">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-221">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de :::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0d9b1-223">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0d9b1-224">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="0d9b1-224">Next steps</span></span>

<span data-ttu-id="0d9b1-225">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="0d9b1-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d9b1-226">Crear un proyecto de aplicación :::no-loc(Blazor WebAssembly)::: hospedado</span><span class="sxs-lookup"><span data-stu-id="0d9b1-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="0d9b1-227">Adición de la biblioteca cliente de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="0d9b1-228">Agregar un concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="0d9b1-229">Agregar servicios de :::no-loc(SignalR)::: y un punto de conexión para el concentrador de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="0d9b1-230">Agregar código de componente de :::no-loc(Razor)::: para chat</span><span class="sxs-lookup"><span data-stu-id="0d9b1-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="0d9b1-231">Para más información sobre la creación de aplicaciones de :::no-loc(Blazor):::, consulte la documentación de :::no-loc(Blazor)::::</span><span class="sxs-lookup"><span data-stu-id="0d9b1-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="0d9b1-232"><xref:blazor/index>
> [Autenticación de tokens de portador con :::no-loc(Identity)::: Server, WebSockets y eventos enviados por el servidor](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="0d9b1-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d9b1-233">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0d9b1-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="0d9b1-234">Negociación entre orígenes de :::no-loc(SignalR)::: para la autenticación</span><span class="sxs-lookup"><span data-stu-id="0d9b1-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
