---
title: 'Tutorial: Introducción a :::no-loc(Razor)::: Pages en ASP.NET Core'
author: rick-anderson
description: 'En esta serie de tutoriales se muestra cómo usar :::no-loc(Razor)::: Pages en ASP.NET Core. Aprenda a crear un modelo, generar código para :::no-loc(Razor)::: Pages, usar Entity Framework Core y SQL Server para el acceso a datos, agregar la funcionalidad de búsqueda, agregar validación de entrada y usar migraciones para actualizar el modelo.'
ms.author: riande
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060240"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="089a9-104">Tutorial: Introducción a :::no-loc(Razor)::: Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="089a9-104">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="089a9-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="089a9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="089a9-106">Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de :::no-loc(Razor)::: Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="089a9-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="089a9-107">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="089a9-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="089a9-108">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="089a9-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="089a9-109">Creará una aplicación web de :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="089a9-109">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="089a9-110">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="089a9-110">Run the app.</span></span>
> * <span data-ttu-id="089a9-111">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-111">Examine the project files.</span></span>

<span data-ttu-id="089a9-112">Al final de este tutorial, tendrá una aplicación web de :::no-loc(Razor)::: Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="089a9-112">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="089a9-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="089a9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="089a9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="089a9-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="089a9-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="089a9-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="089a9-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="089a9-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="089a9-118">Creación de una aplicación web de :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="089a9-118">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="089a9-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="089a9-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="089a9-120">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="089a9-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="089a9-121">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="089a9-122">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="089a9-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="089a9-123">Asigne al proyecto el nombre **:::no-loc(Razor):::PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="089a9-123">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="089a9-124">Es importante asignarle el nombre *:::no-loc(Razor):::PagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="089a9-124">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="089a9-125">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="089a9-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="089a9-126">Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="089a9-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="089a9-128">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="089a9-128">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="089a9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="089a9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="089a9-131">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="089a9-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="089a9-132">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="089a9-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="089a9-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="089a9-134">El comando `dotnet new` crea un proyecto de :::no-loc(Razor)::: Pages en la carpeta *:::no-loc(Razor):::PagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="089a9-134">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="089a9-135">El comando `code` abre la carpeta *:::no-loc(Razor):::PagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="089a9-135">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="089a9-136">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en :::no-loc(Razor):::PagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="089a9-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="089a9-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="089a9-137">Select **Yes**.</span></span>

  <span data-ttu-id="089a9-138">Un directorio *.vscode* , que contiene archivos *launch.json* y *tasks.json* , se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="089a9-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="089a9-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="089a9-140">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="089a9-140">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="089a9-142">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="089a9-143">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="089a9-145">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="089a9-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="089a9-146">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="089a9-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="089a9-147">Si se presenta una opción para seleccionar una **plataforma de destino** , seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="089a9-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="089a9-148">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-148">Select **Next**.</span></span>

* <span data-ttu-id="089a9-149">Asigne al proyecto el nombre **:::no-loc(Razor):::PagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="089a9-149">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="089a9-151">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="089a9-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="089a9-152">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="089a9-152">Examine the project files</span></span>

<span data-ttu-id="089a9-153">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="089a9-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="089a9-154">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="089a9-154">Pages folder</span></span>

<span data-ttu-id="089a9-155">Contiene páginas de :::no-loc(Razor)::: y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="089a9-155">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="089a9-156">Cada página de :::no-loc(Razor)::: consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="089a9-156">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="089a9-157">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="089a9-157">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="089a9-158">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="089a9-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="089a9-159">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="089a9-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="089a9-160">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="089a9-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="089a9-161">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="089a9-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="089a9-162">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="089a9-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="089a9-163">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="089a9-163">wwwroot folder</span></span>

<span data-ttu-id="089a9-164">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="089a9-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="089a9-165">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="089a9-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="089a9-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="089a9-166">appSettings.json</span></span>

<span data-ttu-id="089a9-167">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="089a9-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="089a9-168">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="089a9-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="089a9-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="089a9-169">Program.cs</span></span>

<span data-ttu-id="089a9-170">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="089a9-170">Contains the entry point for the program.</span></span> <span data-ttu-id="089a9-171">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="089a9-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="089a9-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="089a9-172">Startup.cs</span></span>

<span data-ttu-id="089a9-173">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="089a9-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="089a9-174">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="089a9-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="089a9-175">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="089a9-175">Next steps</span></span>

<span data-ttu-id="089a9-176">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="089a9-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="089a9-177">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="089a9-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="089a9-178">Este es el primer tutorial de una serie.</span><span class="sxs-lookup"><span data-stu-id="089a9-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="089a9-179">En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de :::no-loc(Razor)::: Pages en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="089a9-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="089a9-180">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="089a9-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="089a9-181">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="089a9-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="089a9-182">Creará una aplicación web de :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="089a9-182">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="089a9-183">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="089a9-183">Run the app.</span></span>
> * <span data-ttu-id="089a9-184">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-184">Examine the project files.</span></span>

<span data-ttu-id="089a9-185">Al final de este tutorial, tendrá una aplicación web de :::no-loc(Razor)::: Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="089a9-185">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="089a9-187">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="089a9-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="089a9-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="089a9-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="089a9-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="089a9-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="089a9-190">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="089a9-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="089a9-191">Creación de una aplicación web de :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="089a9-191">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="089a9-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="089a9-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="089a9-193">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="089a9-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="089a9-194">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="089a9-196">Asigne al proyecto el nombre **:::no-loc(Razor):::PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="089a9-196">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="089a9-197">Es importante asignarle el nombre *:::no-loc(Razor):::PagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="089a9-197">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="089a9-199">Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="089a9-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="089a9-201">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="089a9-201">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="089a9-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="089a9-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="089a9-204">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="089a9-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="089a9-205">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="089a9-206">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="089a9-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="089a9-207">El comando `dotnet new` crea un proyecto de :::no-loc(Razor)::: Pages en la carpeta *:::no-loc(Razor):::PagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="089a9-207">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="089a9-208">El comando `code` abre la carpeta *:::no-loc(Razor):::PagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="089a9-208">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="089a9-209">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en :::no-loc(Razor):::PagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="089a9-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="089a9-210">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="089a9-210">Select **Yes**.</span></span>

  <span data-ttu-id="089a9-211">Un directorio *.vscode* , que contiene archivos *launch.json* y *tasks.json* , se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="089a9-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="089a9-212">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="089a9-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="089a9-213">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="089a9-213">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="089a9-215">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="089a9-216">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="089a9-217">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):</span><span class="sxs-lookup"><span data-stu-id="089a9-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="089a9-218">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="089a9-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="089a9-219">Si se presenta una opción para seleccionar una **plataforma de destino** , seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="089a9-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="089a9-220">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="089a9-220">Select **Next**.</span></span>

* <span data-ttu-id="089a9-221">Asigne al proyecto el nombre **:::no-loc(Razor):::PagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="089a9-221">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="089a9-223">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="089a9-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="089a9-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="089a9-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="089a9-225">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="089a9-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="089a9-226">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="089a9-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="089a9-227">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="089a9-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="089a9-228">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="089a9-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="089a9-229">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="089a9-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="089a9-230">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="089a9-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="089a9-231">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="089a9-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="089a9-232">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="089a9-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="089a9-234">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="089a9-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="089a9-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="089a9-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="089a9-237">Presione **Ctrl+F5** para ejecutar sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="089a9-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="089a9-238">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="089a9-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="089a9-239">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="089a9-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="089a9-240">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="089a9-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="089a9-241">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="089a9-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="089a9-242">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="089a9-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="089a9-243">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="089a9-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="089a9-245">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="089a9-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="089a9-247">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="089a9-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="089a9-248">Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="089a9-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="089a9-249">Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="089a9-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="089a9-250">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="089a9-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="089a9-251">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="089a9-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="089a9-253">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="089a9-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="089a9-255">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="089a9-255">Examine the project files</span></span>

<span data-ttu-id="089a9-256">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="089a9-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="089a9-257">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="089a9-257">Pages folder</span></span>

<span data-ttu-id="089a9-258">Contiene páginas de :::no-loc(Razor)::: y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="089a9-258">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="089a9-259">Cada página de :::no-loc(Razor)::: consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="089a9-259">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="089a9-260">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="089a9-260">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="089a9-261">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="089a9-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="089a9-262">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="089a9-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="089a9-263">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="089a9-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="089a9-264">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="089a9-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="089a9-265">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="089a9-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="089a9-266">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="089a9-266">wwwroot folder</span></span>

<span data-ttu-id="089a9-267">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="089a9-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="089a9-268">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="089a9-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="089a9-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="089a9-269">appSettings.json</span></span>

<span data-ttu-id="089a9-270">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="089a9-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="089a9-271">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="089a9-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="089a9-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="089a9-272">Program.cs</span></span>

<span data-ttu-id="089a9-273">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="089a9-273">Contains the entry point for the program.</span></span> <span data-ttu-id="089a9-274">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="089a9-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="089a9-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="089a9-275">Startup.cs</span></span>

<span data-ttu-id="089a9-276">Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las :::no-loc(cookie):::s.</span><span class="sxs-lookup"><span data-stu-id="089a9-276">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="089a9-277">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="089a9-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="089a9-278">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="089a9-278">Additional resources</span></span>

* [<span data-ttu-id="089a9-279">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="089a9-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="089a9-280">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="089a9-280">Next steps</span></span>

<span data-ttu-id="089a9-281">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="089a9-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="089a9-282">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="089a9-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
