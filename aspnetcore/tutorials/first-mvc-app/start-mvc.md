---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213082"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d8248-103">Introducción a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d8248-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d8248-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8248-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d8248-105">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d8248-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d8248-106">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="d8248-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="d8248-107">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="d8248-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8248-108">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="d8248-108">Create a web app.</span></span>
> * <span data-ttu-id="d8248-109">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d8248-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d8248-110">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8248-110">Work with a database.</span></span>
> * <span data-ttu-id="d8248-111">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="d8248-111">Add search and validation.</span></span>

<span data-ttu-id="d8248-112">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="d8248-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d8248-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d8248-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d8248-117">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="d8248-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8248-119">En Visual Studio, seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="d8248-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d8248-120">Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d8248-122">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d8248-123">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="d8248-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="d8248-125">Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d8248-126">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8248-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d8248-127">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="d8248-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d8248-128">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="d8248-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d8248-129">Se trata de un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="d8248-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8248-131">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="d8248-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d8248-132">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="d8248-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d8248-133">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d8248-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d8248-134">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="d8248-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d8248-135">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d8248-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d8248-136">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="d8248-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d8248-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="d8248-137">Select **Yes**</span></span>

  * <span data-ttu-id="d8248-138">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d8248-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d8248-139">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d8248-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8248-141">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="d8248-141">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d8248-143">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d8248-144">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d8248-146">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="d8248-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d8248-147">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="d8248-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d8248-148">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="d8248-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="d8248-149">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-149">Select **Next**.</span></span>

* <span data-ttu-id="d8248-150">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d8248-152">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="d8248-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8248-154">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="d8248-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d8248-155">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8248-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d8248-156">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-157">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d8248-158">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="d8248-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d8248-159">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="d8248-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d8248-160">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="d8248-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d8248-161">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="d8248-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d8248-163">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="d8248-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d8248-165">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d8248-165">The following image shows the app:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8248-168">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="d8248-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d8248-169">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d8248-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d8248-170">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-171">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d8248-172">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d8248-173">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="d8248-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d8248-174">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="d8248-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-176">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8248-177">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8248-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d8248-178">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d8248-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d8248-179">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-180">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d8248-181">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="d8248-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d8248-182">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="d8248-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d8248-183">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="d8248-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d8248-184">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d8248-184">The following image shows the app:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d8248-186">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="d8248-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d8248-187">Siguiente</span><span class="sxs-lookup"><span data-stu-id="d8248-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d8248-188">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d8248-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d8248-189">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="d8248-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="d8248-190">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="d8248-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8248-191">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="d8248-191">Create a web app.</span></span>
> * <span data-ttu-id="d8248-192">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d8248-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d8248-193">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="d8248-193">Work with a database.</span></span>
> * <span data-ttu-id="d8248-194">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="d8248-194">Add search and validation.</span></span>

<span data-ttu-id="d8248-195">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="d8248-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d8248-196">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d8248-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="d8248-200">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="d8248-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8248-202">En Visual Studio, seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="d8248-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d8248-203">Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d8248-205">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d8248-206">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="d8248-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="d8248-208">Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d8248-209">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8248-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="d8248-210">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="d8248-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d8248-211">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="d8248-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d8248-212">Se trata de un proyecto introductorio básico, pero es un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="d8248-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8248-214">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="d8248-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d8248-215">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="d8248-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d8248-216">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d8248-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d8248-217">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="d8248-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d8248-218">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d8248-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d8248-219">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="d8248-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d8248-220">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="d8248-220">Select **Yes**</span></span>

  * <span data-ttu-id="d8248-221">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d8248-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d8248-222">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d8248-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-223">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8248-224">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="d8248-224">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d8248-226">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d8248-227">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="d8248-228">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="d8248-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d8248-229">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="d8248-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d8248-230">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="d8248-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="d8248-231">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d8248-231">Select **Next**.</span></span>

* <span data-ttu-id="d8248-232">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d8248-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="d8248-233">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="d8248-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8248-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8248-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8248-235">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="d8248-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d8248-236">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8248-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d8248-237">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-238">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d8248-239">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="d8248-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d8248-240">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="d8248-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d8248-241">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="d8248-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d8248-242">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="d8248-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d8248-244">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="d8248-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="d8248-246">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="d8248-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d8248-247">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="d8248-247">This app doesn't track personal information.</span></span> <span data-ttu-id="d8248-248">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d8248-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="d8248-250">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="d8248-250">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8248-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8248-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8248-253">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="d8248-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d8248-254">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d8248-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d8248-255">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-256">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d8248-257">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d8248-258">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="d8248-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d8248-259">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="d8248-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="d8248-260">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="d8248-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d8248-261">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="d8248-261">This app doesn't track personal information.</span></span> <span data-ttu-id="d8248-262">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d8248-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="d8248-264">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="d8248-264">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8248-266">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8248-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8248-267">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d8248-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d8248-268">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d8248-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d8248-269">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="d8248-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d8248-270">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="d8248-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d8248-271">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="d8248-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d8248-272">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="d8248-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d8248-273">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="d8248-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="d8248-274">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="d8248-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d8248-275">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="d8248-275">This app doesn't track personal information.</span></span> <span data-ttu-id="d8248-276">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d8248-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="d8248-278">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="d8248-278">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d8248-280">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="d8248-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d8248-281">Siguiente</span><span class="sxs-lookup"><span data-stu-id="d8248-281">Next</span></span>](adding-controller.md)

::: moniker-end
