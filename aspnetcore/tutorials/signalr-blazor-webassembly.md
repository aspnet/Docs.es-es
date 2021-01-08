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
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797358"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="faa75-103">Uso de ASP.NET Core SignalR con una aplicación Blazor WebAssembly hospedada</span><span class="sxs-lookup"><span data-stu-id="faa75-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="faa75-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="faa75-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="faa75-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="faa75-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="faa75-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="faa75-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="faa75-107">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="faa75-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="faa75-108">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="faa75-109">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="faa75-110">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="faa75-111">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="faa75-111">Add Razor component code for chat</span></span>

<span data-ttu-id="faa75-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="faa75-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="faa75-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="faa75-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="faa75-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="faa75-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="faa75-116">[Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="faa75-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="faa75-119">Visual Studio para Mac, versión 8.8 o posterior</span><span class="sxs-lookup"><span data-stu-id="faa75-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="faa75-122">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="faa75-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="faa75-125">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="faa75-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-126">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="faa75-127">Creación de un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="faa75-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="faa75-128">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="faa75-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="faa75-130">Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="faa75-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="faa75-131">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="faa75-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="faa75-132">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="faa75-132">Create a new project.</span></span>

1. <span data-ttu-id="faa75-133">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="faa75-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="faa75-134">Escriba `BlazorSignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="faa75-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="faa75-135">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="faa75-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="faa75-136">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="faa75-136">Select **Create**.</span></span>

1. <span data-ttu-id="faa75-137">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="faa75-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="faa75-138">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="faa75-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="faa75-139">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="faa75-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="faa75-141">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="faa75-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="faa75-142">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="faa75-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="faa75-143">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="faa75-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="faa75-144">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="faa75-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="faa75-146">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="faa75-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="faa75-147">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="faa75-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="faa75-148">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="faa75-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="faa75-149">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="faa75-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="faa75-150">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="faa75-150">Select **Next**.</span></span>

1. <span data-ttu-id="faa75-151">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="faa75-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="faa75-152">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="faa75-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="faa75-153">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="faa75-153">Select **Next**.</span></span>

1. <span data-ttu-id="faa75-154">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="faa75-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="faa75-155">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="faa75-155">Select **Create**.</span></span>

   <span data-ttu-id="faa75-156">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="faa75-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="faa75-157">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="faa75-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="faa75-158">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="faa75-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-159">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="faa75-160">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="faa75-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="faa75-161">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="faa75-163">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="faa75-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="faa75-164">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="faa75-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="faa75-165">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="faa75-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="faa75-166">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="faa75-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="faa75-167">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="faa75-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="faa75-168">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="faa75-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="faa75-170">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="faa75-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="faa75-172">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="faa75-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="faa75-173">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="faa75-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="faa75-174">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="faa75-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="faa75-175">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="faa75-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="faa75-176">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="faa75-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-177">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="faa75-178">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="faa75-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="faa75-179">Adición del paquete System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="faa75-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="faa75-180">Debido a un problema de resolución de paquetes al usar [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 en una aplicación ASP.NET Core 3.1, el proyecto de `BlazorSignalRApp.Server` requiere una referencia al paquete para [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="faa75-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="faa75-181">El problema subyacente se resolverá en una versión futura de la revisión de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="faa75-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="faa75-182">Para más información, vea [System.Text.Json define netcoreapp3.0 sin dependencias (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="faa75-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="faa75-183">Para agregar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al proyecto `BlazorSignalRApp.Server` de la solución Blazor hospedada de ASP.NET Core 3.1, siga las instrucciones para elegir las herramientas:</span><span class="sxs-lookup"><span data-stu-id="faa75-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="faa75-185">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Server` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="faa75-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="faa75-186">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="faa75-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="faa75-187">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="faa75-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="faa75-188">En los resultados de la búsqueda, seleccione el paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="faa75-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="faa75-189">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="faa75-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="faa75-190">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="faa75-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="faa75-192">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="faa75-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="faa75-194">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Server` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="faa75-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="faa75-195">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="faa75-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="faa75-196">Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="faa75-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="faa75-197">En los resultados de la búsqueda, active la casilla situada junto al paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="faa75-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="faa75-198">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="faa75-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-199">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="faa75-200">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="faa75-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="faa75-201">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-201">Add a SignalR hub</span></span>

<span data-ttu-id="faa75-202">En el proyecto `BlazorSignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="faa75-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="faa75-203">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="faa75-204">En el proyecto `BlazorSignalRApp.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="faa75-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="faa75-205">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="faa75-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="faa75-206">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="faa75-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="faa75-207">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="faa75-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="faa75-208">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="faa75-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="faa75-209">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="faa75-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="faa75-210">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="faa75-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="faa75-211">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="faa75-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="faa75-212">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="faa75-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="faa75-213">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="faa75-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="faa75-214">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="faa75-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="faa75-215">En el proyecto `BlazorSignalRApp.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="faa75-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="faa75-216">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="faa75-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="faa75-217">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="faa75-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="faa75-218">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="faa75-218">Run the app</span></span>

<span data-ttu-id="faa75-219">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="faa75-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="faa75-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="faa75-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="faa75-221">En el **Explorador de soluciones**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="faa75-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="faa75-222">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="faa75-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="faa75-223">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="faa75-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="faa75-224">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="faa75-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="faa75-225">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="faa75-225">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="faa75-227">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="faa75-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="faa75-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="faa75-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="faa75-229">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="faa75-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="faa75-230">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="faa75-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="faa75-231">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="faa75-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="faa75-232">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="faa75-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="faa75-233">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="faa75-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="faa75-234">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="faa75-234">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="faa75-236">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="faa75-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="faa75-237">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="faa75-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="faa75-238">En la barra lateral **Solución**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="faa75-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="faa75-239">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="faa75-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="faa75-240">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="faa75-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="faa75-241">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="faa75-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="faa75-242">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="faa75-242">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="faa75-244">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="faa75-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="faa75-245">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="faa75-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="faa75-246">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="faa75-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="faa75-247">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="faa75-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="faa75-248">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="faa75-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="faa75-249">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="faa75-249">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="faa75-251">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="faa75-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="faa75-252">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="faa75-252">Next steps</span></span>

<span data-ttu-id="faa75-253">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="faa75-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="faa75-254">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="faa75-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="faa75-255">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="faa75-256">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="faa75-257">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="faa75-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="faa75-258">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="faa75-258">Add Razor component code for chat</span></span>

<span data-ttu-id="faa75-259">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="faa75-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="faa75-260"><xref:blazor/index>
> [Autenticación de tokens de portador con Identity Server, WebSockets y eventos enviados por el servidor](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="faa75-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="faa75-261">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="faa75-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="faa75-262">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="faa75-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
