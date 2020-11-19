---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688530"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="c2566-103">Introducción a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c2566-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="c2566-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c2566-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c2566-105">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c2566-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c2566-106">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="c2566-107">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="c2566-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2566-108">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="c2566-108">Create a web app.</span></span>
> * <span data-ttu-id="c2566-109">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c2566-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c2566-110">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2566-110">Work with a database.</span></span>
> * <span data-ttu-id="c2566-111">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="c2566-111">Add search and validation.</span></span>

<span data-ttu-id="c2566-112">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c2566-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c2566-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="c2566-117">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="c2566-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c2566-119">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c2566-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="c2566-120">En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación web ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="c2566-121">En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `MvcMovie` en **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c2566-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="c2566-122">Es importante usar este nombre exacto, incluido el uso de mayúsculas, para que cada `namespace` coincida cuando se copie el código.</span><span class="sxs-lookup"><span data-stu-id="c2566-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="c2566-123">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-123">Select **Create**.</span></span>
1. <span data-ttu-id="c2566-124">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:</span><span class="sxs-lookup"><span data-stu-id="c2566-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="c2566-125">**.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.</span><span class="sxs-lookup"><span data-stu-id="c2566-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="c2566-126">**Aplicación web de ASP.NET Core (Modelo-Vista-Controlador**).</span><span class="sxs-lookup"><span data-stu-id="c2566-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="c2566-127">**Creación**</span><span class="sxs-lookup"><span data-stu-id="c2566-127">**Create**</span></span>

![<span data-ttu-id="c2566-128">Creación de una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2566-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="c2566-129">Para obtener enfoques alternativos para crear el proyecto, vea [Creación de un proyecto nuevo en Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="c2566-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="c2566-130">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="c2566-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c2566-131">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="c2566-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c2566-132">Se trata de un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="c2566-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-134">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="c2566-135">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="c2566-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c2566-136">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c2566-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c2566-137">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c2566-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c2566-138">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="c2566-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c2566-139">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="c2566-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c2566-140">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="c2566-140">Select **Yes**</span></span>

  * <span data-ttu-id="c2566-141">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="c2566-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c2566-142">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-143">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c2566-144">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="c2566-144">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c2566-146">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c2566-147">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c2566-149">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="c2566-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c2566-150">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="c2566-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c2566-151">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="c2566-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="c2566-152">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-152">Select **Next**.</span></span>

* <span data-ttu-id="c2566-153">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="c2566-155">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="c2566-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2566-157">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="c2566-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c2566-158">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c2566-159">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-160">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-161">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c2566-162">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-163">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c2566-164">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="c2566-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c2566-166">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c2566-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="c2566-168">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2566-168">The following image shows the app:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-171">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="c2566-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c2566-172">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c2566-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c2566-173">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-174">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c2566-175">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c2566-176">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-177">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c2566-180">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c2566-181">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="c2566-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c2566-182">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-183">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-184">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c2566-185">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="c2566-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c2566-186">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="c2566-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="c2566-187">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2566-187">The following image shows the app:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c2566-189">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="c2566-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c2566-190">Siguiente</span><span class="sxs-lookup"><span data-stu-id="c2566-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c2566-191">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c2566-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c2566-192">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="c2566-193">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="c2566-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2566-194">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="c2566-194">Create a web app.</span></span>
> * <span data-ttu-id="c2566-195">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c2566-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c2566-196">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2566-196">Work with a database.</span></span>
> * <span data-ttu-id="c2566-197">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="c2566-197">Add search and validation.</span></span>

<span data-ttu-id="c2566-198">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c2566-199">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c2566-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="c2566-203">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="c2566-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2566-205">En Visual Studio, seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c2566-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c2566-206">Seleccione **Aplicación web de ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c2566-208">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c2566-209">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="c2566-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="c2566-211">Seleccione **Aplicación web (Modelo-Vista-Controlador)** .</span><span class="sxs-lookup"><span data-stu-id="c2566-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="c2566-212">En las listas desplegables, seleccione **.NET Core** y **ASP.NET Core 3.1** y, después, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="c2566-213">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2566-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="c2566-214">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="c2566-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c2566-215">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="c2566-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c2566-216">Se trata de un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="c2566-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-218">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c2566-219">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="c2566-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c2566-220">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c2566-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c2566-221">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c2566-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c2566-222">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="c2566-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c2566-223">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="c2566-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c2566-224">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="c2566-224">Select **Yes**</span></span>

  * <span data-ttu-id="c2566-225">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="c2566-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c2566-226">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-227">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c2566-228">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="c2566-228">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c2566-230">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c2566-231">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c2566-233">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="c2566-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c2566-234">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="c2566-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c2566-235">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="c2566-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="c2566-236">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-236">Select **Next**.</span></span>

* <span data-ttu-id="c2566-237">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="c2566-239">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="c2566-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2566-241">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="c2566-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c2566-242">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c2566-243">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-244">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-245">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c2566-246">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-247">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c2566-248">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="c2566-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c2566-250">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c2566-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="c2566-252">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2566-252">The following image shows the app:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-255">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="c2566-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c2566-256">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c2566-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c2566-257">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-258">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c2566-259">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c2566-260">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-261">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-263">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c2566-264">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c2566-265">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="c2566-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c2566-266">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-267">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-268">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c2566-269">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="c2566-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c2566-270">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="c2566-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="c2566-271">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2566-271">The following image shows the app:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c2566-273">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="c2566-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c2566-274">Siguiente</span><span class="sxs-lookup"><span data-stu-id="c2566-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c2566-275">En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c2566-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c2566-276">La aplicación administra una base de datos de títulos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="c2566-277">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="c2566-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2566-278">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="c2566-278">Create a web app.</span></span>
> * <span data-ttu-id="c2566-279">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c2566-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c2566-280">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2566-280">Work with a database.</span></span>
> * <span data-ttu-id="c2566-281">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="c2566-281">Add search and validation.</span></span>

<span data-ttu-id="c2566-282">Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.</span><span class="sxs-lookup"><span data-stu-id="c2566-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c2566-283">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c2566-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-286">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="c2566-287">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="c2566-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2566-289">En Visual Studio, seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c2566-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c2566-290">Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c2566-292">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c2566-293">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="c2566-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="c2566-295">Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c2566-296">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2566-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="c2566-297">Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear.</span><span class="sxs-lookup"><span data-stu-id="c2566-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c2566-298">Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa.</span><span class="sxs-lookup"><span data-stu-id="c2566-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c2566-299">Se trata de un proyecto introductorio básico, pero es un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="c2566-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-301">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c2566-302">Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).</span><span class="sxs-lookup"><span data-stu-id="c2566-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c2566-303">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c2566-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c2566-304">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c2566-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c2566-305">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="c2566-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c2566-306">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="c2566-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c2566-307">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="c2566-307">Select **Yes**</span></span>

  * <span data-ttu-id="c2566-308">`dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="c2566-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c2566-309">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c2566-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-310">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c2566-311">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="c2566-311">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c2566-313">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c2566-314">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="c2566-315">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="c2566-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c2566-316">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="c2566-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c2566-317">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="c2566-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="c2566-318">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c2566-318">Select **Next**.</span></span>

* <span data-ttu-id="c2566-319">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c2566-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="c2566-320">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="c2566-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2566-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2566-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2566-322">Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.</span><span class="sxs-lookup"><span data-stu-id="c2566-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c2566-323">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c2566-324">Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-325">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-326">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c2566-327">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-328">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c2566-329">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="c2566-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c2566-331">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c2566-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="c2566-333">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="c2566-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c2566-334">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="c2566-334">This app doesn't track personal information.</span></span> <span data-ttu-id="c2566-335">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c2566-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="c2566-337">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="c2566-337">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2566-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2566-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c2566-340">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="c2566-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c2566-341">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c2566-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c2566-342">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-343">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c2566-344">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c2566-345">El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="c2566-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c2566-346">Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="c2566-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="c2566-347">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="c2566-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c2566-348">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="c2566-348">This app doesn't track personal information.</span></span> <span data-ttu-id="c2566-349">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c2566-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="c2566-351">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="c2566-351">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2566-353">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2566-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c2566-354">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2566-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c2566-355">Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="c2566-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c2566-356">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="c2566-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c2566-357">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="c2566-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c2566-358">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="c2566-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c2566-359">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="c2566-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c2566-360">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="c2566-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="c2566-361">Seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="c2566-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c2566-362">Esta aplicación no lleva un seguimiento de la información personal.</span><span class="sxs-lookup"><span data-stu-id="c2566-362">This app doesn't track personal information.</span></span> <span data-ttu-id="c2566-363">El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c2566-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="c2566-365">En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:</span><span class="sxs-lookup"><span data-stu-id="c2566-365">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c2566-367">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="c2566-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c2566-368">Siguiente</span><span class="sxs-lookup"><span data-stu-id="c2566-368">Next</span></span>](adding-controller.md)

::: moniker-end
