---
title: Uso de ASP.NET Core SignalR con Blazor
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536407"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="3c815-103">Uso de ASP.NET Core SignalR con Blazor</span><span class="sxs-lookup"><span data-stu-id="3c815-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="3c815-104">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor.</span><span class="sxs-lookup"><span data-stu-id="3c815-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="3c815-105">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="3c815-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c815-106">Crear un proyecto Blazor</span><span class="sxs-lookup"><span data-stu-id="3c815-106">Create a Blazor project</span></span>
> * <span data-ttu-id="3c815-107">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="3c815-108">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="3c815-109">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3c815-110">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="3c815-110">Add Razor component code for chat</span></span>

<span data-ttu-id="3c815-111">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="3c815-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="3c815-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3c815-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c815-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="3c815-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c815-115">[Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3c815-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3c815-118">Visual Studio para Mac, versión 8.8 o posterior</span><span class="sxs-lookup"><span data-stu-id="3c815-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-119">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c815-121">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3c815-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3c815-124">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="3c815-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-125">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="3c815-126">Creación de una aplicación Blazor WebAssembly hospedada</span><span class="sxs-lookup"><span data-stu-id="3c815-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="3c815-127">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="3c815-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3c815-129">Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="3c815-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3c815-130">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="3c815-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="3c815-131">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-131">Create a new project.</span></span>

1. <span data-ttu-id="3c815-132">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3c815-133">Escriba `BlazorWebAssemblySignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="3c815-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="3c815-134">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3c815-135">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-135">Select **Create**.</span></span>

1. <span data-ttu-id="3c815-136">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="3c815-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="3c815-137">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="3c815-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3c815-138">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3c815-140">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="3c815-141">La opción `-ho|--hosted` crea una solución hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3c815-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="3c815-142">Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="3c815-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="3c815-143">La opción `-o|--output` crea una carpeta para la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="3c815-144">Si ha creado una carpeta para la solución y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="3c815-145">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3c815-146">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="3c815-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3c815-147">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="3c815-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-149">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3c815-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="3c815-150">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="3c815-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3c815-151">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="3c815-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="3c815-152">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="3c815-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3c815-153">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-153">Select **Next**.</span></span>

1. <span data-ttu-id="3c815-154">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="3c815-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3c815-155">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="3c815-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="3c815-156">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-156">Select **Next**.</span></span>

1. <span data-ttu-id="3c815-157">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorWebAssemblySignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="3c815-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="3c815-158">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-158">Select **Create**.</span></span>

   <span data-ttu-id="3c815-159">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="3c815-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3c815-160">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="3c815-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="3c815-161">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="3c815-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-162">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-163">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="3c815-164">La opción `-ho|--hosted` crea una solución hospedada Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3c815-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="3c815-165">La opción `-o|--output` crea una carpeta para la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="3c815-166">Si ha creado una carpeta para la solución y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3c815-167">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3c815-169">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-170">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-171">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3c815-172">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="3c815-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3c815-173">Establezca la versión para que coincida con el marco compartido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3c815-174">Seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-174">Select **Install**.</span></span>

1. <span data-ttu-id="3c815-175">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3c815-176">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3c815-178">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3c815-179">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-180">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-181">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-182">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-183">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3c815-184">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="3c815-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3c815-185">Establezca la versión para que coincida con el marco compartido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3c815-186">Seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="3c815-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="3c815-187">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-188">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-189">En un shell de comandos de la carpeta de la solución, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3c815-190">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="3c815-191">Adición del paquete System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="3c815-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="3c815-192">*Esta sección solo se aplica a las aplicaciones para ASP.NET Core versión 3.x.*</span><span class="sxs-lookup"><span data-stu-id="3c815-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="3c815-193">Debido a un problema de resolución de paquetes al usar [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x en una aplicación ASP.NET Core 3.x, el proyecto `BlazorWebAssemblySignalRApp.Server` requiere una referencia al paquete para [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="3c815-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="3c815-194">El problema subyacente se resolverá en una versión futura de la revisión de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="3c815-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="3c815-195">Para más información, vea [System.Text.Json define netcoreapp3.0 sin dependencias (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="3c815-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="3c815-196">Para agregar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al proyecto `BlazorWebAssemblySignalRApp.Server` de la solución Blazor hospedada de ASP.NET Core 3.1, siga las instrucciones para elegir las herramientas:</span><span class="sxs-lookup"><span data-stu-id="3c815-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3c815-198">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Server` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-199">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-200">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3c815-201">En los resultados de la búsqueda, seleccione el paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="3c815-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="3c815-202">Seleccione la versión del paquete que coincida con el marco compartido en uso.</span><span class="sxs-lookup"><span data-stu-id="3c815-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="3c815-203">Seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-203">Select **Install**.</span></span>

1. <span data-ttu-id="3c815-204">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3c815-205">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3c815-207">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="3c815-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="3c815-208">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-210">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Server` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-211">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-212">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3c815-213">En los resultados de la búsqueda, active la casilla situada junto al paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), seleccione la versión correcta del paquete que coincida con el marco compartido en uso y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="3c815-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="3c815-214">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-215">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-216">En un shell de comandos de la carpeta de la solución, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="3c815-217">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3c815-218">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-218">Add a SignalR hub</span></span>

<span data-ttu-id="3c815-219">En el proyecto `BlazorWebAssemblySignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="3c815-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3c815-220">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3c815-221">En el proyecto `BlazorWebAssemblySignalRApp.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="3c815-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="3c815-222">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="3c815-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3c815-223">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c815-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="3c815-224">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c815-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3c815-225">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="3c815-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3c815-226">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="3c815-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3c815-227">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c815-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="3c815-228">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c815-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3c815-229">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="3c815-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3c815-230">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="3c815-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3c815-231">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="3c815-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3c815-232">En el proyecto `BlazorWebAssemblySignalRApp.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="3c815-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3c815-233">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3c815-234">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="3c815-235">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="3c815-235">Run the app</span></span>

<span data-ttu-id="3c815-236">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="3c815-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3c815-238">En el **Explorador de soluciones**, seleccione el proyecto `BlazorWebAssemblySignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="3c815-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="3c815-239">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-240">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-241">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-242">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-242">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-244">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c815-246">Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="3c815-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="3c815-247">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-248">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-249">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-250">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-250">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-252">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-253">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-254">En la barra lateral **Solución**, seleccione el proyecto `BlazorWebAssemblySignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="3c815-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="3c815-255">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-256">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-257">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-258">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-258">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-260">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-261">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3c815-262">En un shell de comandos de la carpeta de la solución, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3c815-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="3c815-263">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-264">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-265">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-265">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-267">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="3c815-268">Creación de una aplicación Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3c815-268">Create a Blazor Server app</span></span>

<span data-ttu-id="3c815-269">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="3c815-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3c815-271">Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="3c815-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3c815-272">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="3c815-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="3c815-273">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-273">Create a new project.</span></span>

1. <span data-ttu-id="3c815-274">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3c815-275">Escriba `BlazorServerSignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="3c815-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="3c815-276">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3c815-277">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-277">Select **Create**.</span></span>

1. <span data-ttu-id="3c815-278">Elija la plantilla **Aplicación de Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3c815-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="3c815-279">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3c815-281">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="3c815-282">La opción `-o|--output` crea una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="3c815-283">Si ha creado una carpeta para el proyecto y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="3c815-284">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3c815-285">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="3c815-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3c815-286">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="3c815-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="3c815-287">Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="3c815-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-288">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-289">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3c815-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="3c815-290">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="3c815-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3c815-291">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="3c815-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="3c815-292">Elija la plantilla **Aplicación de Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3c815-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3c815-293">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-293">Select **Next**.</span></span>

1. <span data-ttu-id="3c815-294">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="3c815-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3c815-295">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3c815-295">Select **Next**.</span></span>

1. <span data-ttu-id="3c815-296">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorServerSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="3c815-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="3c815-297">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3c815-297">Select **Create**.</span></span>

   <span data-ttu-id="3c815-298">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="3c815-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3c815-299">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="3c815-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="3c815-300">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="3c815-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-301">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-302">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="3c815-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="3c815-303">La opción `-o|--output` crea una carpeta para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3c815-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="3c815-304">Si ha creado una carpeta para el proyecto y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.</span><span class="sxs-lookup"><span data-stu-id="3c815-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3c815-305">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3c815-307">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-308">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-309">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3c815-310">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="3c815-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3c815-311">Establezca la versión para que coincida con el marco compartido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3c815-312">Seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-312">Select **Install**.</span></span>

1. <span data-ttu-id="3c815-313">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3c815-314">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3c815-316">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3c815-317">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-318">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-319">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-320">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-321">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3c815-322">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="3c815-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3c815-323">Establezca la versión para que coincida con el marco compartido de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3c815-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3c815-324">Seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="3c815-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="3c815-325">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-326">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-327">En un shell de comandos de la carpeta del proyecto, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3c815-328">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="3c815-329">Adición del paquete System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="3c815-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="3c815-330">*Esta sección solo se aplica a las aplicaciones para ASP.NET Core versión 3.x.*</span><span class="sxs-lookup"><span data-stu-id="3c815-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="3c815-331">Debido a un problema de resolución de paquetes al usar [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x en una aplicación ASP.NET Core 3.x, el proyecto requiere una referencia al paquete para [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="3c815-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="3c815-332">El problema subyacente se resolverá en una versión futura de la revisión de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="3c815-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="3c815-333">Para más información, vea [System.Text.Json define netcoreapp3.0 sin dependencias (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="3c815-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="3c815-334">Para agregar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al proyecto, siga las instrucciones correspondientes a la herramienta de su elección:</span><span class="sxs-lookup"><span data-stu-id="3c815-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3c815-336">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-337">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-338">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3c815-339">En los resultados de la búsqueda, seleccione el paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="3c815-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="3c815-340">Seleccione la versión del paquete que coincida con el marco compartido en uso.</span><span class="sxs-lookup"><span data-stu-id="3c815-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="3c815-341">Seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-341">Select **Install**.</span></span>

1. <span data-ttu-id="3c815-342">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3c815-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3c815-343">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3c815-345">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="3c815-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="3c815-346">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-347">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-348">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3c815-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3c815-349">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="3c815-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3c815-350">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="3c815-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3c815-351">En los resultados de la búsqueda, active la casilla situada junto al paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), seleccione la versión correcta del paquete que coincida con el marco compartido en uso y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="3c815-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="3c815-352">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="3c815-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-353">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3c815-354">En un shell de comandos de la carpeta del proyecto, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="3c815-355">Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.</span><span class="sxs-lookup"><span data-stu-id="3c815-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3c815-356">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-356">Add a SignalR hub</span></span>

<span data-ttu-id="3c815-357">Cree una carpeta `Hubs` (en plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="3c815-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3c815-358">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3c815-359">Abra el archivo `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="3c815-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="3c815-360">Agregue los espacios de nombres para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> y la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="3c815-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3c815-361">Agregue servicios de middleware de compresión de respuesta a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c815-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="3c815-362">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c815-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3c815-363">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="3c815-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3c815-364">Entre los puntos de conexión para asignar el concentrador Blazor y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="3c815-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3c815-365">Agregue servicios de middleware de compresión de respuesta a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c815-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="3c815-366">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3c815-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3c815-367">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="3c815-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3c815-368">Entre los puntos de conexión para asignar el concentrador Blazor y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="3c815-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3c815-369">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="3c815-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3c815-370">Abra el archivo `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="3c815-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3c815-371">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3c815-372">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3c815-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="3c815-373">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="3c815-373">Run the app</span></span>

<span data-ttu-id="3c815-374">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="3c815-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c815-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c815-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3c815-376">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-377">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-378">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-379">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-379">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-381">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c815-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c815-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3c815-383">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-384">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-385">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-386">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-386">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-388">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c815-389">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3c815-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3c815-390">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="3c815-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3c815-391">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-392">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-393">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-393">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-395">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3c815-396">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c815-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3c815-397">En un shell de comandos de la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3c815-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="3c815-398">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="3c815-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3c815-399">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="3c815-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3c815-400">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="3c815-400">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3c815-402">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3c815-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="3c815-403">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="3c815-403">Next steps</span></span>

<span data-ttu-id="3c815-404">En este tutorial, ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="3c815-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c815-405">Crear un proyecto Blazor</span><span class="sxs-lookup"><span data-stu-id="3c815-405">Create a Blazor project</span></span>
> * <span data-ttu-id="3c815-406">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="3c815-407">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="3c815-408">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="3c815-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3c815-409">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="3c815-409">Add Razor component code for chat</span></span>

<span data-ttu-id="3c815-410">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="3c815-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="3c815-411"><xref:blazor/index>
> [Autenticación de tokens de portador con Identity Server, WebSockets y eventos enviados por el servidor](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="3c815-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c815-412">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3c815-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="3c815-413">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="3c815-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
