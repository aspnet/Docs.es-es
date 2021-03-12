---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589782"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="af9e2-103">Introducción a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="af9e2-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="af9e2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af9e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="af9e2-105">Este es el primer tutorial de una serie que muestra el desarrollo web de ASP.NET Core MVC con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="af9e2-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="af9e2-106">Al final de la serie, tendrá una aplicación que administra y muestra datos de películas.</span><span class="sxs-lookup"><span data-stu-id="af9e2-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="af9e2-107">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="af9e2-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af9e2-108">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-108">Create a web app.</span></span>
> * <span data-ttu-id="af9e2-109">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="af9e2-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="af9e2-110">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="af9e2-110">Work with a database.</span></span>
> * <span data-ttu-id="af9e2-111">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-111">Add search and validation.</span></span>

<span data-ttu-id="af9e2-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af9e2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af9e2-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="af9e2-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="af9e2-117">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="af9e2-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af9e2-119">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="af9e2-120">En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación web ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="af9e2-121">En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `MvcMovie` en **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="af9e2-122">Es importante asignar al proyecto el nombre *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="af9e2-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="af9e2-123">Las mayúsculas tienen que coincidir con cada correspondencia de `namespace` cuando se copia el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="af9e2-124">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-124">Select **Create**.</span></span>
* <span data-ttu-id="af9e2-125">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:</span><span class="sxs-lookup"><span data-stu-id="af9e2-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="af9e2-126">**.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.</span><span class="sxs-lookup"><span data-stu-id="af9e2-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="af9e2-127">**Aplicación web de ASP.NET Core (Modelo-Vista-Controlador**).</span><span class="sxs-lookup"><span data-stu-id="af9e2-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="af9e2-128">**Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-128">**Create**.</span></span>

![<span data-ttu-id="af9e2-129">Creación de una aplicación web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af9e2-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="af9e2-130">Para obtener enfoques alternativos para crear el proyecto, vea [Creación de un proyecto nuevo en Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="af9e2-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="af9e2-131">Visual Studio usó la plantilla de proyecto predeterminada para el proyecto de MVC creado.</span><span class="sxs-lookup"><span data-stu-id="af9e2-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="af9e2-132">El proyecto creado:</span><span class="sxs-lookup"><span data-stu-id="af9e2-132">The created project:</span></span>

* <span data-ttu-id="af9e2-133">Es una aplicación de trabajo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-133">Is a working app.</span></span>
* <span data-ttu-id="af9e2-134">Es un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="af9e2-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="af9e2-136">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="af9e2-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="af9e2-137">Para más información, vea [Introducción a VS Code](https://code.visualstudio.com/docs) y [Ayuda de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="af9e2-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="af9e2-138">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="af9e2-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="af9e2-139">Cambie al directorio (`cd`) que contendrá el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af9e2-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="af9e2-140">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="af9e2-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="af9e2-141">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "MvcMovie". ¿Desea agregarlos?), seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="af9e2-142">`dotnet new mvc -o MvcMovie`; Crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="af9e2-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="af9e2-143">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="af9e2-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="af9e2-145">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-145">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="af9e2-147">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="af9e2-148">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="af9e2-150">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="af9e2-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="af9e2-151">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="af9e2-152">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="af9e2-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="af9e2-153">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-153">Select **Next**.</span></span>

* <span data-ttu-id="af9e2-154">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="af9e2-156">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="af9e2-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af9e2-158">Seleccione Ctrl + F5 para ejecutar la aplicación sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="af9e2-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="af9e2-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af9e2-159">Visual Studio:</span></span>

  * <span data-ttu-id="af9e2-160">Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="af9e2-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="af9e2-161">Ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-161">Runs the app.</span></span>

  <span data-ttu-id="af9e2-162">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-163">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-164">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="af9e2-165">El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:</span><span class="sxs-lookup"><span data-stu-id="af9e2-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="af9e2-166">Modifique el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-166">Make code changes.</span></span>
* <span data-ttu-id="af9e2-167">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-167">Save the file.</span></span>
* <span data-ttu-id="af9e2-168">Actualizar rápidamente el explorador y ver los cambios en el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="af9e2-169">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="af9e2-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menú Depurar](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="af9e2-171">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="af9e2-173">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="af9e2-173">The following image shows the app:</span></span>

![Página Inicio o Índice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="af9e2-176">Seleccione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="af9e2-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="af9e2-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="af9e2-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="af9e2-178">Inicia [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="af9e2-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="af9e2-179">Inicia un explorador.</span><span class="sxs-lookup"><span data-stu-id="af9e2-179">Launches a browser.</span></span>
  * <span data-ttu-id="af9e2-180">Navega a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="af9e2-181">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-182">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-183">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="af9e2-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="af9e2-184">El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:</span><span class="sxs-lookup"><span data-stu-id="af9e2-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="af9e2-185">Modifique el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-185">Make code changes.</span></span>
* <span data-ttu-id="af9e2-186">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-186">Save the file.</span></span>
* <span data-ttu-id="af9e2-187">Actualizar rápidamente el explorador y ver los cambios en el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="af9e2-190">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="af9e2-191">Visual Studio para Mac:</span><span class="sxs-lookup"><span data-stu-id="af9e2-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="af9e2-192">Inicia el servidor de [Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="af9e2-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="af9e2-193">Inicia un explorador.</span><span class="sxs-lookup"><span data-stu-id="af9e2-193">Launches a browser.</span></span>
  * <span data-ttu-id="af9e2-194">Navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="af9e2-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="af9e2-195">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-196">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-197">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="af9e2-198">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="af9e2-199">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="af9e2-199">The following image shows the app:</span></span>

![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="af9e2-201">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="af9e2-202">Siguiente: Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="af9e2-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="af9e2-203">Este es el primer tutorial de una serie que muestra el desarrollo web de ASP.NET Core MVC con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="af9e2-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="af9e2-204">Al final de la serie, tendrá una aplicación que administra y muestra datos de películas.</span><span class="sxs-lookup"><span data-stu-id="af9e2-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="af9e2-205">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="af9e2-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af9e2-206">Crear una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-206">Create a web app.</span></span>
> * <span data-ttu-id="af9e2-207">Agregar un modelo y aplicarle scaffolding.</span><span class="sxs-lookup"><span data-stu-id="af9e2-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="af9e2-208">Trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="af9e2-208">Work with a database.</span></span>
> * <span data-ttu-id="af9e2-209">Agregar búsqueda y validación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-209">Add search and validation.</span></span>

<span data-ttu-id="af9e2-210">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af9e2-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af9e2-211">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="af9e2-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-214">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="af9e2-215">Creación de una aplicación web</span><span class="sxs-lookup"><span data-stu-id="af9e2-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af9e2-217">En Visual Studio, seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="af9e2-218">Seleccione **Aplicación web de ASP.NET Core** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Creación de un proyecto de aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="af9e2-220">Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="af9e2-221">Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="af9e2-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Configuración del nuevo proyecto](start-mvc/_static/config.png)

* <span data-ttu-id="af9e2-223">Seleccione **Aplicación web (Modelo-Vista-Controlador)** .</span><span class="sxs-lookup"><span data-stu-id="af9e2-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="af9e2-224">En las listas desplegables, seleccione **.NET Core** y **ASP.NET Core 3.1** y, después, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="af9e2-225">Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af9e2-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="af9e2-226">Visual Studio usó la plantilla de proyecto predeterminada para el proyecto de MVC creado.</span><span class="sxs-lookup"><span data-stu-id="af9e2-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="af9e2-227">El proyecto creado:</span><span class="sxs-lookup"><span data-stu-id="af9e2-227">The created project:</span></span>

* <span data-ttu-id="af9e2-228">Es una aplicación de trabajo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-228">Is a working app.</span></span>
* <span data-ttu-id="af9e2-229">Es un proyecto básico de inicio.</span><span class="sxs-lookup"><span data-stu-id="af9e2-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="af9e2-231">Para realizar el tutorial debe estar familiarizado con VS Code.</span><span class="sxs-lookup"><span data-stu-id="af9e2-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="af9e2-232">Para más información, vea [Introducción a VS Code](https://code.visualstudio.com/docs) y [Ayuda de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="af9e2-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="af9e2-233">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="af9e2-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="af9e2-234">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af9e2-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="af9e2-235">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="af9e2-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="af9e2-236">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "MvcMovie". ¿Desea agregarlos?), seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="af9e2-237">`dotnet new mvc -o MvcMovie`; Crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="af9e2-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="af9e2-238">`code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="af9e2-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-239">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="af9e2-240">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-240">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="af9e2-242">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="af9e2-243">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="af9e2-245">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="af9e2-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="af9e2-246">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="af9e2-247">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="af9e2-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="af9e2-248">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-248">Select **Next**.</span></span>

* <span data-ttu-id="af9e2-249">Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="af9e2-251">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="af9e2-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af9e2-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af9e2-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af9e2-253">Seleccione CTRL+F5 para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="af9e2-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="af9e2-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af9e2-254">Visual Studio:</span></span>

  * <span data-ttu-id="af9e2-255">Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="af9e2-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="af9e2-256">Ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-256">Runs the app.</span></span>

  <span data-ttu-id="af9e2-257">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-258">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-259">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="af9e2-260">El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:</span><span class="sxs-lookup"><span data-stu-id="af9e2-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="af9e2-261">Modifique el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-261">Make code changes.</span></span>
* <span data-ttu-id="af9e2-262">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-262">Save the file.</span></span>
* <span data-ttu-id="af9e2-263">Actualizar rápidamente el explorador y ver los cambios en el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="af9e2-264">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="af9e2-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menú Depurar](start-mvc/_static/debug_menu.png)

<span data-ttu-id="af9e2-266">Puede depurar la aplicación seleccionando el botón **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="af9e2-268">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="af9e2-268">The following image shows the app:</span></span>

![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="af9e2-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af9e2-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="af9e2-271">Seleccione CTRL+F5 para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="af9e2-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="af9e2-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="af9e2-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="af9e2-273">Inicia [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="af9e2-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="af9e2-274">Inicia un explorador.</span><span class="sxs-lookup"><span data-stu-id="af9e2-274">Launches a browser.</span></span>
  * <span data-ttu-id="af9e2-275">Navega a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="af9e2-276">En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-277">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-278">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="af9e2-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="af9e2-279">El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:</span><span class="sxs-lookup"><span data-stu-id="af9e2-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="af9e2-280">Modifique el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-280">Make code changes.</span></span>
* <span data-ttu-id="af9e2-281">Guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="af9e2-281">Save the file.</span></span>
* <span data-ttu-id="af9e2-282">Actualizar rápidamente el explorador y ver los cambios en el código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af9e2-284">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="af9e2-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="af9e2-285">Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af9e2-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="af9e2-286">Visual Studio para Mac: inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="af9e2-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="af9e2-287">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="af9e2-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af9e2-288">El nombre de host estándar del equipo local es `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af9e2-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="af9e2-289">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="af9e2-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="af9e2-290">Al ejecutar la aplicación verá otro puerto distinto.</span><span class="sxs-lookup"><span data-stu-id="af9e2-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="af9e2-291">Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.</span><span class="sxs-lookup"><span data-stu-id="af9e2-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="af9e2-292">En la imagen siguiente se muestra la aplicación:</span><span class="sxs-lookup"><span data-stu-id="af9e2-292">The following image shows the app:</span></span>

![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="af9e2-294">En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.</span><span class="sxs-lookup"><span data-stu-id="af9e2-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="af9e2-295">Siguiente</span><span class="sxs-lookup"><span data-stu-id="af9e2-295">Next</span></span>](adding-controller.md)

::: moniker-end
