---
title: Uso de ASP.NET Core SignalR con una aplicación Blazor WebAssembly hospedada
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2975ee699a2535cdf63ef2f5af5790f178a09f93
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637735"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="b8471-103">Uso de ASP.NET Core SignalR con una aplicación Blazor WebAssembly hospedada</span><span class="sxs-lookup"><span data-stu-id="b8471-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="b8471-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8471-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8471-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b8471-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="b8471-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="b8471-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b8471-107">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="b8471-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="b8471-108">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="b8471-109">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="b8471-110">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="b8471-111">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="b8471-111">Add Razor component code for chat</span></span>

<span data-ttu-id="b8471-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="b8471-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="b8471-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8471-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b8471-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="b8471-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="b8471-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8471-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8471-116">[Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="b8471-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8471-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8471-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8471-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8471-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b8471-119">Visual Studio para Mac, versión 8.8 o posterior</span><span class="sxs-lookup"><span data-stu-id="b8471-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="b8471-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8471-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="b8471-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8471-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8471-122">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="b8471-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8471-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8471-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8471-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8471-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b8471-125">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="b8471-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="b8471-126">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8471-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="b8471-127">Creación de un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="b8471-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="b8471-128">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="b8471-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8471-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8471-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b8471-130">Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="b8471-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b8471-131">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="b8471-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="b8471-132">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="b8471-132">Create a new project.</span></span>

1. <span data-ttu-id="b8471-133">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b8471-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="b8471-134">Escriba `BlazorSignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="b8471-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="b8471-135">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b8471-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b8471-136">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b8471-136">Select **Create**.</span></span>

1. <span data-ttu-id="b8471-137">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b8471-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="b8471-138">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="b8471-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="b8471-139">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b8471-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8471-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8471-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b8471-141">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b8471-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="b8471-142">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b8471-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="b8471-143">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="b8471-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="b8471-144">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="b8471-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8471-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8471-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b8471-146">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="b8471-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="b8471-147">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="b8471-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b8471-148">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="b8471-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="b8471-149">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b8471-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b8471-150">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b8471-150">Select **Next**.</span></span>

1. <span data-ttu-id="b8471-151">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="b8471-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="b8471-152">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="b8471-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="b8471-153">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b8471-153">Select **Next**.</span></span>

1. <span data-ttu-id="b8471-154">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="b8471-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="b8471-155">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b8471-155">Select **Create**.</span></span>

   <span data-ttu-id="b8471-156">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="b8471-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b8471-157">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="b8471-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="b8471-158">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="b8471-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b8471-159">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8471-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b8471-160">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b8471-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="b8471-161">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8471-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8471-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="b8471-163">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b8471-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="b8471-164">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="b8471-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="b8471-165">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="b8471-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="b8471-166">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="b8471-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="b8471-167">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="b8471-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="b8471-168">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="b8471-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8471-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8471-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="b8471-170">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="b8471-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8471-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8471-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b8471-172">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b8471-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="b8471-173">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="b8471-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="b8471-174">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="b8471-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="b8471-175">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="b8471-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="b8471-176">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="b8471-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b8471-177">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8471-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b8471-178">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="b8471-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="b8471-179">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-179">Add a SignalR hub</span></span>

<span data-ttu-id="b8471-180">En el proyecto `BlazorSignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8471-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="b8471-181">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="b8471-182">En el proyecto `BlazorSignalRApp.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="b8471-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="b8471-183">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="b8471-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="b8471-184">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b8471-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="b8471-185">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b8471-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="b8471-186">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="b8471-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="b8471-187">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="b8471-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="b8471-188">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="b8471-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="b8471-189">En el proyecto `BlazorSignalRApp.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="b8471-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="b8471-190">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b8471-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="b8471-191">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="b8471-191">Run the app</span></span>

1. <span data-ttu-id="b8471-192">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="b8471-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8471-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8471-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b8471-194">En el **Explorador de soluciones**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="b8471-194">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="b8471-195">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="b8471-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b8471-196">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="b8471-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b8471-197">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="b8471-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b8471-198">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="b8471-198">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="b8471-200">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b8471-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8471-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8471-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b8471-202">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="b8471-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

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

1. <span data-ttu-id="b8471-203">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="b8471-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b8471-204">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="b8471-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b8471-205">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="b8471-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b8471-206">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="b8471-206">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="b8471-208">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b8471-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8471-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8471-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b8471-210">En la barra lateral **Solución**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="b8471-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="b8471-211">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="b8471-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b8471-212">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="b8471-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b8471-213">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="b8471-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b8471-214">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="b8471-214">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="b8471-216">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b8471-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b8471-217">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8471-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b8471-218">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="b8471-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="b8471-219">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="b8471-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b8471-220">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="b8471-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b8471-221">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="b8471-221">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="b8471-223">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b8471-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="b8471-224">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b8471-224">Next steps</span></span>

<span data-ttu-id="b8471-225">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="b8471-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b8471-226">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="b8471-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="b8471-227">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="b8471-228">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="b8471-229">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="b8471-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="b8471-230">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="b8471-230">Add Razor component code for chat</span></span>

<span data-ttu-id="b8471-231">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="b8471-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="b8471-232"><xref:blazor/index>
> [Autenticación de tokens de portador con Identity Server, WebSockets y eventos enviados por el servidor](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="b8471-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8471-233">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b8471-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="b8471-234">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="b8471-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
