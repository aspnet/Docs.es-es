---
title: 'Tutorial: Introducción a Razor Pages en ASP.NET Core'
author: rick-anderson
description: Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486231"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="53ca9-103">Tutorial: Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53ca9-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="53ca9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53ca9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="53ca9-105">Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53ca9-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="53ca9-106">Para una introducción más avanzada dirigida a los desarrolladores que están familiarizados con los controladores y las vistas, consulte [Introducción a Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="53ca9-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="53ca9-107">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="53ca9-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="53ca9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="53ca9-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="53ca9-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="53ca9-110">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="53ca9-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="53ca9-111">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-111">Run the app.</span></span>
> * <span data-ttu-id="53ca9-112">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-112">Examine the project files.</span></span>

<span data-ttu-id="53ca9-113">Al final de este tutorial, tendrá una aplicación web de Razor Pages que mejorará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="53ca9-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="53ca9-115">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="53ca9-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="53ca9-119">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="53ca9-121">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="53ca9-122">Para obtener más información, consulte [Creación de un proyecto en Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="53ca9-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Creación de un proyecto nuevo desde la ventana de inicio](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="53ca9-124">En el cuadro de diálogo **Crear un proyecto nuevo**, seleccione **Aplicación web ASP.NET Core** y, a continuación, seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="53ca9-126">En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `RazorPagesMovie` en **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="53ca9-127">Es importante asignarle el nombre *RazorPagesMovie* al proyecto (respetando mayúsculas y minúsculas) para que los espacios de nombres coincidan al copiar y pegar el código de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="53ca9-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="53ca9-128">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-128">Select **Create**.</span></span>

    ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="53ca9-130">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:</span><span class="sxs-lookup"><span data-stu-id="53ca9-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="53ca9-131">**.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.</span><span class="sxs-lookup"><span data-stu-id="53ca9-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="53ca9-132">**Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-132">**Web Application**.</span></span>
    1. <span data-ttu-id="53ca9-133">**Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-133">**Create**.</span></span>

     ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="53ca9-135">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="53ca9-135">The following starter project is created:</span></span>

    ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="53ca9-138">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="53ca9-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="53ca9-139">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="53ca9-140">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="53ca9-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="53ca9-141">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="53ca9-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="53ca9-142">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="53ca9-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-143">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="53ca9-144">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-144">Select **File** > **New Solution**.</span></span>

    ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="53ca9-146">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="53ca9-147">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="53ca9-149">En el cuadro de diálogo de **configuración de la nueva aplicación web**:</span><span class="sxs-lookup"><span data-stu-id="53ca9-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="53ca9-150">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="53ca9-151">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión .NET 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="53ca9-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="53ca9-152">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-152">Select **Next**.</span></span>

1. <span data-ttu-id="53ca9-153">Asigne al proyecto el nombre *RazorPagesMovie* y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![Nombre del proyecto en macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="53ca9-155">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="53ca9-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="53ca9-156">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="53ca9-156">Examine the project files</span></span>

<span data-ttu-id="53ca9-157">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="53ca9-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="53ca9-158">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-158">Pages folder</span></span>

<span data-ttu-id="53ca9-159">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="53ca9-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="53ca9-160">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="53ca9-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="53ca9-161">Un archivo *.cshtml* que tiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="53ca9-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="53ca9-162">Un archivo *.cshtml.cs* que tiene código C# que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="53ca9-163">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="53ca9-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="53ca9-164">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="53ca9-165">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="53ca9-166">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="53ca9-167">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="53ca9-167">wwwroot folder</span></span>

<span data-ttu-id="53ca9-168">Contiene los recursos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="53ca9-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="53ca9-169">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="53ca9-170">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="53ca9-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="53ca9-171">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="53ca9-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-172">Program.cs</span></span>

<span data-ttu-id="53ca9-173">Contiene el punto de entrada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-173">Contains the entry point for the app.</span></span> <span data-ttu-id="53ca9-174">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="53ca9-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-175">Startup.cs</span></span>

<span data-ttu-id="53ca9-176">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="53ca9-177">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53ca9-178">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="53ca9-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="53ca9-179">Siguiente: Adición de un modelo</span><span class="sxs-lookup"><span data-stu-id="53ca9-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="53ca9-180">Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53ca9-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="53ca9-181">Para una introducción más avanzada dirigida a los desarrolladores que están familiarizados con los controladores y las vistas, consulte [Introducción a Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="53ca9-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="53ca9-182">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="53ca9-183">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="53ca9-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="53ca9-184">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="53ca9-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="53ca9-185">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="53ca9-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="53ca9-186">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-186">Run the app.</span></span>
> * <span data-ttu-id="53ca9-187">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-187">Examine the project files.</span></span>

<span data-ttu-id="53ca9-188">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="53ca9-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="53ca9-190">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="53ca9-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="53ca9-194">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53ca9-196">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="53ca9-197">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="53ca9-198">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="53ca9-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="53ca9-199">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="53ca9-200">Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="53ca9-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="53ca9-201">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="53ca9-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="53ca9-202">Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="53ca9-204">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="53ca9-204">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="53ca9-207">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="53ca9-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="53ca9-208">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="53ca9-209">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="53ca9-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="53ca9-210">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="53ca9-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="53ca9-211">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="53ca9-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="53ca9-212">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="53ca9-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="53ca9-213">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-213">Select **Yes**.</span></span>

  <span data-ttu-id="53ca9-214">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53ca9-216">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-216">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="53ca9-218">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="53ca9-219">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="53ca9-221">En el cuadro de diálogo de **configuración de la nueva aplicación web**:</span><span class="sxs-lookup"><span data-stu-id="53ca9-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="53ca9-222">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="53ca9-223">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="53ca9-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="53ca9-224">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-224">Select **Next**.</span></span>

* <span data-ttu-id="53ca9-225">Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Nombre del proyecto en macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="53ca9-227">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="53ca9-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="53ca9-228">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="53ca9-228">Examine the project files</span></span>

<span data-ttu-id="53ca9-229">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="53ca9-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="53ca9-230">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-230">Pages folder</span></span>

<span data-ttu-id="53ca9-231">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="53ca9-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="53ca9-232">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="53ca9-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="53ca9-233">Un archivo *.cshtml* que tiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="53ca9-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="53ca9-234">Un archivo *.cshtml.cs* que tiene código C# que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="53ca9-235">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="53ca9-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="53ca9-236">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="53ca9-237">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="53ca9-238">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="53ca9-239">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="53ca9-239">wwwroot folder</span></span>

<span data-ttu-id="53ca9-240">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="53ca9-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="53ca9-241">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="53ca9-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="53ca9-242">appSettings.json</span></span>

<span data-ttu-id="53ca9-243">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="53ca9-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="53ca9-244">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="53ca9-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-245">Program.cs</span></span>

<span data-ttu-id="53ca9-246">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="53ca9-246">Contains the entry point for the program.</span></span> <span data-ttu-id="53ca9-247">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="53ca9-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-248">Startup.cs</span></span>

<span data-ttu-id="53ca9-249">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="53ca9-250">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53ca9-251">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="53ca9-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="53ca9-252">Siguiente: Adición de un modelo</span><span class="sxs-lookup"><span data-stu-id="53ca9-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53ca9-253">Este es el primer tutorial de una serie.</span><span class="sxs-lookup"><span data-stu-id="53ca9-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="53ca9-254">En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de Razor Pages en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53ca9-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="53ca9-255">Para una introducción más avanzada dirigida a los desarrolladores que están familiarizados con los controladores y las vistas, consulte [Introducción a Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="53ca9-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="53ca9-256">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="53ca9-257">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="53ca9-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="53ca9-258">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="53ca9-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="53ca9-259">Creará una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="53ca9-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="53ca9-260">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-260">Run the app.</span></span>
> * <span data-ttu-id="53ca9-261">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-261">Examine the project files.</span></span>

<span data-ttu-id="53ca9-262">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="53ca9-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="53ca9-264">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="53ca9-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-267">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="53ca9-268">Creación de una aplicación web de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53ca9-270">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="53ca9-271">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="53ca9-273">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="53ca9-274">Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="53ca9-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="53ca9-276">Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="53ca9-278">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="53ca9-278">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="53ca9-281">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="53ca9-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="53ca9-282">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="53ca9-283">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="53ca9-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="53ca9-284">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="53ca9-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="53ca9-285">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="53ca9-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="53ca9-286">Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="53ca9-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="53ca9-287">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-287">Select **Yes**.</span></span>

  <span data-ttu-id="53ca9-288">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="53ca9-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-289">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53ca9-290">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-290">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="53ca9-292">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="53ca9-293">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="53ca9-294">En el cuadro de diálogo de **configuración de la nueva aplicación web**:</span><span class="sxs-lookup"><span data-stu-id="53ca9-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="53ca9-295">Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="53ca9-296">Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="53ca9-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="53ca9-297">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-297">Select **Next**.</span></span>

* <span data-ttu-id="53ca9-298">Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="53ca9-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="53ca9-300">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="53ca9-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53ca9-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53ca9-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53ca9-302">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="53ca9-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="53ca9-303">Iniciar la aplicación con <kbd>CTRL+F5</kbd> (modo de no depuración) le permite efectuar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="53ca9-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="53ca9-304">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="53ca9-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="53ca9-305">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="53ca9-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="53ca9-306">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="53ca9-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="53ca9-307">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="53ca9-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="53ca9-308">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="53ca9-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="53ca9-309">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="53ca9-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="53ca9-310">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="53ca9-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="53ca9-311">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="53ca9-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="53ca9-313">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="53ca9-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="53ca9-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53ca9-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="53ca9-316">Presione <kbd>Ctrl+F5</kbd> para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="53ca9-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="53ca9-317">Iniciar la aplicación con <kbd>CTRL+F5</kbd> (modo de no depuración) le permite efectuar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="53ca9-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="53ca9-318">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="53ca9-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="53ca9-319">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador y se desplaza hasta `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="53ca9-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="53ca9-320">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="53ca9-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="53ca9-321">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="53ca9-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="53ca9-322">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="53ca9-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="53ca9-323">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="53ca9-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="53ca9-324">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="53ca9-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="53ca9-326">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="53ca9-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53ca9-328">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53ca9-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="53ca9-329">Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="53ca9-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="53ca9-330">El inicio de la aplicación con <kbd>Cmd+Opt+F5</kbd> (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código.</span><span class="sxs-lookup"><span data-stu-id="53ca9-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="53ca9-331">Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.</span><span class="sxs-lookup"><span data-stu-id="53ca9-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="53ca9-332">Visual Studio inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador y se desplaza a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="53ca9-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="53ca9-333">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="53ca9-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="53ca9-334">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="53ca9-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="53ca9-336">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="53ca9-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home (Inicio) o página ::: no-LOC (index):::](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="53ca9-338">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="53ca9-338">Examine the project files</span></span>

<span data-ttu-id="53ca9-339">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="53ca9-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="53ca9-340">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="53ca9-340">Pages folder</span></span>

<span data-ttu-id="53ca9-341">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="53ca9-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="53ca9-342">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="53ca9-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="53ca9-343">Un archivo *.cshtml* que tiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="53ca9-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="53ca9-344">Un archivo *.cshtml.cs* que tiene código C# que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="53ca9-345">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="53ca9-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="53ca9-346">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="53ca9-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="53ca9-347">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="53ca9-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="53ca9-348">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="53ca9-349">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="53ca9-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="53ca9-350">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="53ca9-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="53ca9-351">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="53ca9-351">wwwroot folder</span></span>

<span data-ttu-id="53ca9-352">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="53ca9-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="53ca9-353">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="53ca9-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="53ca9-354">appSettings.json</span></span>

<span data-ttu-id="53ca9-355">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="53ca9-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="53ca9-356">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="53ca9-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-357">Program.cs</span></span>

<span data-ttu-id="53ca9-358">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="53ca9-358">Contains the entry point for the program.</span></span> <span data-ttu-id="53ca9-359">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="53ca9-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="53ca9-360">Startup.cs</span></span>

<span data-ttu-id="53ca9-361">Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las cookies.</span><span class="sxs-lookup"><span data-stu-id="53ca9-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="53ca9-362">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="53ca9-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53ca9-363">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="53ca9-363">Additional resources</span></span>

* [<span data-ttu-id="53ca9-364">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="53ca9-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="53ca9-365">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="53ca9-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="53ca9-366">Siguiente: Adición de un modelo</span><span class="sxs-lookup"><span data-stu-id="53ca9-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
