---
title: 'Tutorial: Introducción a Razor Pages en ASP.NET Core'
author: rick-anderson
description: En esta serie de tutoriales se muestra cómo usar Razor Pages en ASP.NET Core. Aprenda a crear un modelo, generar código para Razor Pages, usar Entity Framework Core y SQL Server para el acceso a datos, agregar la funcionalidad de búsqueda, agregar validación de entrada y usar migraciones para actualizar el modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405307"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1c9aa-104">Tutorial: Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c9aa-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1c9aa-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c9aa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1c9aa-106">Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1c9aa-107">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1c9aa-108">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1c9aa-109">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1c9aa-110">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-110">Run the app.</span></span>
> * <span data-ttu-id="1c9aa-111">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-111">Examine the project files.</span></span>

<span data-ttu-id="1c9aa-112">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1c9aa-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1c9aa-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c9aa-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c9aa-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c9aa-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c9aa-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1c9aa-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c9aa-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1c9aa-118">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1c9aa-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c9aa-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c9aa-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c9aa-120">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1c9aa-121">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1c9aa-122">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1c9aa-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1c9aa-123">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1c9aa-124">Es importante asignarle el nombre *RazorPagesMovie* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1c9aa-125">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1c9aa-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1c9aa-126">Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1c9aa-128">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-128">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c9aa-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c9aa-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c9aa-131">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1c9aa-132">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1c9aa-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1c9aa-134">El comando `dotnet new` crea un proyecto de Razor en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1c9aa-135">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1c9aa-136">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo que pregunta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1c9aa-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-137">Select **Yes**.</span></span>

  <span data-ttu-id="1c9aa-138">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1c9aa-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c9aa-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c9aa-140">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-140">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1c9aa-142">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1c9aa-143">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="1c9aa-145">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="1c9aa-146">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="1c9aa-147">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="1c9aa-148">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-148">Select **Next**.</span></span>

  ![Selección de .NET Core 3.1 de macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1c9aa-150">Asigne el nombre **RazorPagesMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1c9aa-152">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="1c9aa-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="1c9aa-153">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="1c9aa-153">Examine the project files</span></span>

<span data-ttu-id="1c9aa-154">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1c9aa-155">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="1c9aa-155">Pages folder</span></span>

<span data-ttu-id="1c9aa-156">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1c9aa-157">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1c9aa-158">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1c9aa-159">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1c9aa-160">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1c9aa-161">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1c9aa-162">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1c9aa-163">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1c9aa-164">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="1c9aa-164">wwwroot folder</span></span>

<span data-ttu-id="1c9aa-165">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1c9aa-166">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1c9aa-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1c9aa-167">appSettings.json</span></span>

<span data-ttu-id="1c9aa-168">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1c9aa-169">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1c9aa-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1c9aa-170">Program.cs</span></span>

<span data-ttu-id="1c9aa-171">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-171">Contains the entry point for the program.</span></span> <span data-ttu-id="1c9aa-172">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1c9aa-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1c9aa-173">Startup.cs</span></span>

<span data-ttu-id="1c9aa-174">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="1c9aa-175">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c9aa-176">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="1c9aa-176">Next steps</span></span>

<span data-ttu-id="1c9aa-177">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1c9aa-178">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="1c9aa-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c9aa-179">Este es el primer tutorial de una serie.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="1c9aa-180">En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de Razor Pages en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1c9aa-181">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1c9aa-182">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1c9aa-183">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1c9aa-184">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-184">Run the app.</span></span>
> * <span data-ttu-id="1c9aa-185">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-185">Examine the project files.</span></span>

<span data-ttu-id="1c9aa-186">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1c9aa-188">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1c9aa-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c9aa-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c9aa-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c9aa-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c9aa-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1c9aa-191">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c9aa-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1c9aa-192">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1c9aa-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c9aa-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c9aa-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c9aa-194">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1c9aa-195">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1c9aa-197">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1c9aa-198">Es importante asignarle el nombre *RazorPagesMovie* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="1c9aa-200">Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1c9aa-202">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-202">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1c9aa-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c9aa-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1c9aa-205">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1c9aa-206">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1c9aa-207">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1c9aa-208">El comando `dotnet new` crea un proyecto de Razor en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1c9aa-209">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1c9aa-210">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo que pregunta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1c9aa-211">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-211">Select **Yes**.</span></span>

  <span data-ttu-id="1c9aa-212">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1c9aa-213">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c9aa-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c9aa-214">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-214">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1c9aa-216">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="1c9aa-217">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="1c9aa-218">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), establezca la **plataforma de destino** en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Selección de .NET Core 3.0 de macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1c9aa-220">Asigne el nombre **RazorPagesMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1c9aa-222">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="1c9aa-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c9aa-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c9aa-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c9aa-224">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1c9aa-225">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1c9aa-226">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1c9aa-227">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1c9aa-228">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1c9aa-229">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1c9aa-230">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1c9aa-231">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1c9aa-233">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="1c9aa-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c9aa-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1c9aa-236">Presione **Ctrl+F5** para ejecutar sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1c9aa-237">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1c9aa-238">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="1c9aa-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1c9aa-239">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1c9aa-240">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1c9aa-241">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1c9aa-242">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1c9aa-244">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1c9aa-246">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c9aa-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1c9aa-247">Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1c9aa-248">Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1c9aa-249">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1c9aa-250">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1c9aa-252">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1c9aa-254">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="1c9aa-254">Examine the project files</span></span>

<span data-ttu-id="1c9aa-255">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1c9aa-256">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="1c9aa-256">Pages folder</span></span>

<span data-ttu-id="1c9aa-257">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1c9aa-258">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1c9aa-259">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1c9aa-260">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1c9aa-261">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1c9aa-262">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1c9aa-263">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1c9aa-264">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1c9aa-265">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="1c9aa-265">wwwroot folder</span></span>

<span data-ttu-id="1c9aa-266">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1c9aa-267">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1c9aa-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1c9aa-268">appSettings.json</span></span>

<span data-ttu-id="1c9aa-269">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1c9aa-270">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1c9aa-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1c9aa-271">Program.cs</span></span>

<span data-ttu-id="1c9aa-272">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-272">Contains the entry point for the program.</span></span> <span data-ttu-id="1c9aa-273">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1c9aa-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1c9aa-274">Startup.cs</span></span>

<span data-ttu-id="1c9aa-275">Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las cookies.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1c9aa-276">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1c9aa-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c9aa-277">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1c9aa-277">Additional resources</span></span>

* [<span data-ttu-id="1c9aa-278">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="1c9aa-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1c9aa-279">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="1c9aa-279">Next steps</span></span>

<span data-ttu-id="1c9aa-280">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="1c9aa-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1c9aa-281">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="1c9aa-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
