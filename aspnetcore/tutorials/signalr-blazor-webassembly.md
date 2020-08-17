---
title: Uso de ASP.NET Core SignalR con Blazor WebAssembly
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021632"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="9a704-103">Uso de ASP.NET Core SignalR con Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9a704-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="9a704-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9a704-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9a704-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9a704-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="9a704-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="9a704-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9a704-107">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="9a704-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="9a704-108">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="9a704-109">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="9a704-110">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9a704-111">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="9a704-111">Add Razor component code for chat</span></span>

<span data-ttu-id="9a704-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="9a704-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="9a704-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a704-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9a704-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="9a704-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a704-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a704-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a704-116">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="9a704-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a704-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a704-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a704-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a704-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9a704-119">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="9a704-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="9a704-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a704-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="9a704-121">Creación de un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="9a704-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="9a704-122">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="9a704-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a704-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a704-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="9a704-124">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="9a704-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="9a704-125">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="9a704-125">Create a new project.</span></span>

1. <span data-ttu-id="9a704-126">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="9a704-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="9a704-127">Escriba `BlazorSignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="9a704-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="9a704-128">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9a704-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9a704-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a704-129">Select **Create**.</span></span>

1. <span data-ttu-id="9a704-130">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="9a704-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="9a704-131">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="9a704-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="9a704-132">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a704-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a704-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a704-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9a704-134">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a704-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="9a704-135">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a704-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="9a704-136">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="9a704-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="9a704-137">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="9a704-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a704-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a704-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9a704-139">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a704-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="9a704-140">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="9a704-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="9a704-141">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="9a704-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="9a704-142">Elija la plantilla **Aplicación de Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="9a704-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="9a704-143">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="9a704-143">Select **Next**.</span></span>

1. <span data-ttu-id="9a704-144">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="9a704-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="9a704-145">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="9a704-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="9a704-146">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="9a704-146">Select **Next**.</span></span>

1. <span data-ttu-id="9a704-147">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="9a704-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="9a704-148">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a704-148">Select **Create**.</span></span>

   <span data-ttu-id="9a704-149">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="9a704-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="9a704-150">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="9a704-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="9a704-151">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="9a704-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9a704-152">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a704-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9a704-153">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a704-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="9a704-154">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a704-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a704-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9a704-156">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="9a704-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9a704-157">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="9a704-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9a704-158">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="9a704-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9a704-159">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="9a704-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="9a704-160">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="9a704-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9a704-161">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="9a704-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a704-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a704-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9a704-163">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a704-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a704-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a704-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9a704-165">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="9a704-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9a704-166">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="9a704-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="9a704-167">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="9a704-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="9a704-168">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="9a704-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="9a704-169">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="9a704-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9a704-170">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a704-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9a704-171">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a704-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="9a704-172">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-172">Add a SignalR hub</span></span>

<span data-ttu-id="9a704-173">En el proyecto `BlazorSignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="9a704-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="9a704-174">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="9a704-175">En el proyecto `BlazorSignalRApp.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="9a704-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="9a704-176">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="9a704-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="9a704-177">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a704-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="9a704-178">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9a704-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="9a704-179">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="9a704-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="9a704-180">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="9a704-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="9a704-181">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="9a704-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="9a704-182">En el proyecto `BlazorSignalRApp.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="9a704-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="9a704-183">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a704-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="9a704-184">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="9a704-184">Run the app</span></span>

1. <span data-ttu-id="9a704-185">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="9a704-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a704-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a704-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9a704-187">En el **Explorador de soluciones**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="9a704-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="9a704-188">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="9a704-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9a704-189">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="9a704-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9a704-190">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="9a704-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9a704-191">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="9a704-191">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9a704-193">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9a704-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a704-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a704-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9a704-195">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="9a704-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="9a704-196">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="9a704-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9a704-197">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="9a704-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9a704-198">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="9a704-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9a704-199">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="9a704-199">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9a704-201">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9a704-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a704-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a704-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9a704-203">En la barra lateral **Solución**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="9a704-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="9a704-204">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="9a704-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9a704-205">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="9a704-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9a704-206">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="9a704-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9a704-207">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="9a704-207">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9a704-209">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9a704-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9a704-210">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a704-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9a704-211">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a704-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="9a704-212">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="9a704-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9a704-213">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="9a704-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="9a704-214">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="9a704-214">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9a704-216">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9a704-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="9a704-217">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="9a704-217">Next steps</span></span>

<span data-ttu-id="9a704-218">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="9a704-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9a704-219">Crear un proyecto de aplicación Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="9a704-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="9a704-220">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="9a704-221">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="9a704-222">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="9a704-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9a704-223">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="9a704-223">Add Razor component code for chat</span></span>

<span data-ttu-id="9a704-224">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="9a704-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="9a704-225">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9a704-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="9a704-226">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="9a704-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
