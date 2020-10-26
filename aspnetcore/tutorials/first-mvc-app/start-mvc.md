---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 177112106d143a6826c1f927aac807da0aa9f2b4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113834"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="6038b-103">Introducción a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6038b-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="6038b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6038b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6038b-105">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6038b-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="6038b-106">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="6038b-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="6038b-107">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="6038b-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6038b-108">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="6038b-108">Create a web app.</span></span>
> * <span data-ttu-id="6038b-109">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6038b-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="6038b-110">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="6038b-110">Work with a database.</span></span>
> * <span data-ttu-id="6038b-111">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="6038b-111">Add search and validation.</span></span>

<span data-ttu-id="6038b-112">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="6038b-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="6038b-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="6038b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="6038b-117">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="6038b-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6038b-119">En Visual Studio, seleccione **Crear un proyecto** .</span><span class="sxs-lookup"><span data-stu-id="6038b-119">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="6038b-120">Seleccione **Aplicación web de ASP.NET Core** > **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-120">Select **ASP.NET Core Web Application** > **Next** .</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="6038b-122">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-122">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="6038b-123">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="6038b-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="6038b-125">Seleccione **Aplicación web (Modelo-Vista-Controlador)** .</span><span class="sxs-lookup"><span data-stu-id="6038b-125">Select **Web Application(Model-View-Controller)** .</span></span> <span data-ttu-id="6038b-126">En las listas desplegables, seleccione **.NET Core** y **ASP.NET Core 3.1** y, después, **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create** .</span></span>

![<span data-ttu-id="6038b-127">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6038b-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="6038b-128">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="6038b-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="6038b-129">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="6038b-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6038b-130">Se trata de un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="6038b-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6038b-132">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="6038b-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="6038b-133">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="6038b-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="6038b-134">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6038b-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6038b-135">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6038b-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6038b-136">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="6038b-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="6038b-137">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="6038b-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="6038b-138">Seleccione **Sí** .</span><span class="sxs-lookup"><span data-stu-id="6038b-138">Select **Yes**</span></span>

  * <span data-ttu-id="6038b-139">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="6038b-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="6038b-140">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6038b-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-141">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6038b-142">Seleccione **Archivo** > **Nueva solución** .</span><span class="sxs-lookup"><span data-stu-id="6038b-142">Select **File** > **New Solution** .</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6038b-144">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="6038b-145">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="6038b-147">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="6038b-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="6038b-148">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación** .</span><span class="sxs-lookup"><span data-stu-id="6038b-148">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="6038b-149">Si se presenta una opción para seleccionar una **plataforma de destino** , seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="6038b-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="6038b-150">Seleccione **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-150">Select **Next** .</span></span>

* <span data-ttu-id="6038b-151">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-151">Name the project **MvcMovie** , and then select **Create** .</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="6038b-153">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="6038b-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6038b-155">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="6038b-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="6038b-156">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6038b-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="6038b-157">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-158">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6038b-159">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="6038b-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="6038b-160">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="6038b-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6038b-161">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="6038b-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6038b-162">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="6038b-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6038b-164">Puede depurar la aplicación seleccionando el botón **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6038b-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="6038b-166">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6038b-166">The following image shows the app:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6038b-169">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="6038b-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="6038b-170">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6038b-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="6038b-171">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-172">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="6038b-173">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="6038b-174">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="6038b-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6038b-175">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="6038b-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6038b-178">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6038b-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="6038b-179">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6038b-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="6038b-180">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-181">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6038b-182">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="6038b-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6038b-183">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="6038b-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6038b-184">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="6038b-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="6038b-185">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="6038b-185">The following image shows the app:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="6038b-187">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="6038b-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6038b-188">Siguiente</span><span class="sxs-lookup"><span data-stu-id="6038b-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6038b-189">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6038b-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="6038b-190">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="6038b-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="6038b-191">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="6038b-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6038b-192">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="6038b-192">Create a web app.</span></span>
> * <span data-ttu-id="6038b-193">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6038b-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="6038b-194">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="6038b-194">Work with a database.</span></span>
> * <span data-ttu-id="6038b-195">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="6038b-195">Add search and validation.</span></span>

<span data-ttu-id="6038b-196">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="6038b-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="6038b-197">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="6038b-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="6038b-201">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="6038b-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6038b-203">En Visual Studio, seleccione **Crear un proyecto** .</span><span class="sxs-lookup"><span data-stu-id="6038b-203">From the Visual Studio select **Create a new project** .</span></span>

* <span data-ttu-id="6038b-204">Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-204">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="6038b-206">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-206">Name the project **MvcMovie** and select **Create** .</span></span> <span data-ttu-id="6038b-207">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="6038b-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="6038b-209">Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-209">Select **Web Application(Model-View-Controller)** , and then select **Create** .</span></span>

![<span data-ttu-id="6038b-210">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6038b-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="6038b-211">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="6038b-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="6038b-212">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="6038b-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6038b-213">Se trata de un proyecto introductorio básico, pero es un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="6038b-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6038b-215">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="6038b-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="6038b-216">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="6038b-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="6038b-217">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6038b-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6038b-218">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6038b-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6038b-219">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="6038b-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="6038b-220">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="6038b-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="6038b-221">Seleccione **Sí** .</span><span class="sxs-lookup"><span data-stu-id="6038b-221">Select **Yes**</span></span>

  * <span data-ttu-id="6038b-222">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="6038b-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="6038b-223">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6038b-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-224">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6038b-225">Seleccione **Archivo** > **Nueva solución** .</span><span class="sxs-lookup"><span data-stu-id="6038b-225">Select **File** > **New Solution** .</span></span>

  ![macOS: Nueva solución](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6038b-227">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span> <span data-ttu-id="6038b-228">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next** .</span></span>

* <span data-ttu-id="6038b-229">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="6038b-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="6038b-230">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación** .</span><span class="sxs-lookup"><span data-stu-id="6038b-230">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="6038b-231">Si se presenta una opción para seleccionar una **plataforma de destino** , seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="6038b-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="6038b-232">Seleccione **Siguiente** .</span><span class="sxs-lookup"><span data-stu-id="6038b-232">Select **Next** .</span></span>

* <span data-ttu-id="6038b-233">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear** .</span><span class="sxs-lookup"><span data-stu-id="6038b-233">Name the project **MvcMovie** , and then select **Create** .</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="6038b-234">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="6038b-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6038b-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6038b-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6038b-236">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="6038b-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="6038b-237">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6038b-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="6038b-238">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-239">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6038b-240">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="6038b-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="6038b-241">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="6038b-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6038b-242">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="6038b-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6038b-243">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar** :</span><span class="sxs-lookup"><span data-stu-id="6038b-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6038b-245">Puede depurar la aplicación seleccionando el botón **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6038b-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="6038b-247">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="6038b-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6038b-248">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="6038b-248">This app doesn't track personal information.</span></span> <span data-ttu-id="6038b-249">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6038b-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="6038b-251">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="6038b-251">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6038b-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6038b-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6038b-254">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="6038b-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="6038b-255">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6038b-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="6038b-256">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-257">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="6038b-258">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="6038b-259">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="6038b-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6038b-260">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="6038b-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="6038b-261">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="6038b-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6038b-262">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="6038b-262">This app doesn't track personal information.</span></span> <span data-ttu-id="6038b-263">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6038b-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="6038b-265">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="6038b-265">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6038b-267">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6038b-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6038b-268">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6038b-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="6038b-269">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6038b-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="6038b-270">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="6038b-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6038b-271">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="6038b-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6038b-272">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="6038b-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6038b-273">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="6038b-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6038b-274">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="6038b-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="6038b-275">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="6038b-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6038b-276">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="6038b-276">This app doesn't track personal information.</span></span> <span data-ttu-id="6038b-277">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6038b-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="6038b-279">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="6038b-279">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="6038b-281">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="6038b-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6038b-282">Siguiente</span><span class="sxs-lookup"><span data-stu-id="6038b-282">Next</span></span>](adding-controller.md)

::: moniker-end
