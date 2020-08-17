---
title: 'Tutorial: Introducción a Razor Pages en ASP.NET Core'
author: rick-anderson
description: En esta serie de tutoriales se muestra cómo usar Razor Pages en ASP.NET Core. Aprenda a crear un modelo, generar código para Razor Pages, usar Entity Framework Core y SQL Server para el acceso a datos, agregar la funcionalidad de búsqueda, agregar validación de entrada y usar migraciones para actualizar el modelo.
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021099"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="1e64a-104">Tutorial: Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e64a-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1e64a-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e64a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1e64a-106">Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e64a-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1e64a-107">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="1e64a-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1e64a-108">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="1e64a-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e64a-109">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1e64a-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1e64a-110">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1e64a-110">Run the app.</span></span>
> * <span data-ttu-id="1e64a-111">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-111">Examine the project files.</span></span>

<span data-ttu-id="1e64a-112">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="1e64a-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1e64a-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1e64a-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e64a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e64a-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e64a-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e64a-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e64a-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1e64a-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="1e64a-118">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1e64a-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e64a-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e64a-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e64a-120">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1e64a-121">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1e64a-122">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1e64a-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1e64a-123">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1e64a-124">Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="1e64a-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1e64a-125">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1e64a-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1e64a-126">Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1e64a-128">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="1e64a-128">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e64a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e64a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e64a-131">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1e64a-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1e64a-132">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1e64a-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1e64a-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1e64a-134">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="1e64a-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1e64a-135">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1e64a-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1e64a-136">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="1e64a-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1e64a-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-137">Select **Yes**.</span></span>

  <span data-ttu-id="1e64a-138">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e64a-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1e64a-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e64a-140">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-140">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1e64a-142">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1e64a-143">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="1e64a-145">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="1e64a-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="1e64a-146">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="1e64a-147">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="1e64a-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="1e64a-148">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-148">Select **Next**.</span></span>

* <span data-ttu-id="1e64a-149">Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1e64a-151">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="1e64a-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="1e64a-152">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="1e64a-152">Examine the project files</span></span>

<span data-ttu-id="1e64a-153">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="1e64a-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1e64a-154">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="1e64a-154">Pages folder</span></span>

<span data-ttu-id="1e64a-155">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="1e64a-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1e64a-156">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="1e64a-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1e64a-157">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="1e64a-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1e64a-158">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="1e64a-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1e64a-159">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="1e64a-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1e64a-160">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="1e64a-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1e64a-161">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="1e64a-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1e64a-162">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1e64a-163">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="1e64a-163">wwwroot folder</span></span>

<span data-ttu-id="1e64a-164">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="1e64a-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1e64a-165">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1e64a-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1e64a-166">appSettings.json</span></span>

<span data-ttu-id="1e64a-167">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="1e64a-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1e64a-168">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1e64a-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1e64a-169">Program.cs</span></span>

<span data-ttu-id="1e64a-170">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="1e64a-170">Contains the entry point for the program.</span></span> <span data-ttu-id="1e64a-171">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1e64a-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1e64a-172">Startup.cs</span></span>

<span data-ttu-id="1e64a-173">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1e64a-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="1e64a-174">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1e64a-175">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="1e64a-175">Next steps</span></span>

<span data-ttu-id="1e64a-176">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="1e64a-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1e64a-177">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="1e64a-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e64a-178">Este es el primer tutorial de una serie.</span><span class="sxs-lookup"><span data-stu-id="1e64a-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="1e64a-179">En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de Razor Pages en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e64a-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1e64a-180">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="1e64a-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1e64a-181">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="1e64a-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e64a-182">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1e64a-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1e64a-183">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1e64a-183">Run the app.</span></span>
> * <span data-ttu-id="1e64a-184">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-184">Examine the project files.</span></span>

<span data-ttu-id="1e64a-185">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="1e64a-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1e64a-187">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1e64a-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e64a-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e64a-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e64a-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e64a-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e64a-190">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1e64a-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="1e64a-191">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1e64a-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e64a-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e64a-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e64a-193">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1e64a-194">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1e64a-196">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1e64a-197">Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="1e64a-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="1e64a-199">Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1e64a-201">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="1e64a-201">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e64a-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e64a-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e64a-204">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1e64a-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1e64a-205">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1e64a-206">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1e64a-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1e64a-207">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="1e64a-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1e64a-208">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1e64a-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1e64a-209">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="1e64a-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1e64a-210">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-210">Select **Yes**.</span></span>

  <span data-ttu-id="1e64a-211">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1e64a-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e64a-212">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1e64a-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e64a-213">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-213">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1e64a-215">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1e64a-216">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="1e64a-217">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="1e64a-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="1e64a-218">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="1e64a-219">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="1e64a-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="1e64a-220">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-220">Select **Next**.</span></span>

* <span data-ttu-id="1e64a-221">Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1e64a-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1e64a-223">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="1e64a-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e64a-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e64a-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e64a-225">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1e64a-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1e64a-226">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1e64a-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1e64a-227">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="1e64a-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1e64a-228">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1e64a-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1e64a-229">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1e64a-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1e64a-230">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="1e64a-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1e64a-231">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1e64a-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1e64a-232">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1e64a-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1e64a-234">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1e64a-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="1e64a-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e64a-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1e64a-237">Presione **Ctrl+F5** para ejecutar sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1e64a-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1e64a-238">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1e64a-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1e64a-239">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="1e64a-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1e64a-240">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1e64a-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1e64a-241">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1e64a-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1e64a-242">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1e64a-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1e64a-243">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1e64a-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1e64a-245">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1e64a-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e64a-247">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1e64a-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1e64a-248">Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1e64a-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1e64a-249">Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1e64a-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1e64a-250">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1e64a-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1e64a-251">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1e64a-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1e64a-253">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1e64a-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1e64a-255">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="1e64a-255">Examine the project files</span></span>

<span data-ttu-id="1e64a-256">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="1e64a-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1e64a-257">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="1e64a-257">Pages folder</span></span>

<span data-ttu-id="1e64a-258">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="1e64a-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1e64a-259">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="1e64a-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1e64a-260">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="1e64a-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1e64a-261">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="1e64a-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1e64a-262">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="1e64a-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1e64a-263">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="1e64a-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1e64a-264">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="1e64a-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1e64a-265">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1e64a-266">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="1e64a-266">wwwroot folder</span></span>

<span data-ttu-id="1e64a-267">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="1e64a-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1e64a-268">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1e64a-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1e64a-269">appSettings.json</span></span>

<span data-ttu-id="1e64a-270">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="1e64a-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1e64a-271">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1e64a-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1e64a-272">Program.cs</span></span>

<span data-ttu-id="1e64a-273">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="1e64a-273">Contains the entry point for the program.</span></span> <span data-ttu-id="1e64a-274">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1e64a-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1e64a-275">Startup.cs</span></span>

<span data-ttu-id="1e64a-276">Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las cookies.</span><span class="sxs-lookup"><span data-stu-id="1e64a-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1e64a-277">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1e64a-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e64a-278">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1e64a-278">Additional resources</span></span>

* [<span data-ttu-id="1e64a-279">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="1e64a-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1e64a-280">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="1e64a-280">Next steps</span></span>

<span data-ttu-id="1e64a-281">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="1e64a-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1e64a-282">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="1e64a-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
