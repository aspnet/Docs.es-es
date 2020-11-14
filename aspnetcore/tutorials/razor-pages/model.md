---
title: 'Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core'
author: rick-anderson
description: 'Parte 2 de la serie de tutoriales sobre Razor Pages.'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050932"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="1f8d3-103">Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f8d3-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="1f8d3-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f8d3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="1f8d3-105">En esta sección, se agregan clases para administrar películas.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="1f8d3-106">Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="1f8d3-107">EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="1f8d3-108">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1f8d3-109">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1f8d3-110">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="1f8d3-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-112">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="1f8d3-113">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-113">Name the folder *Models*.</span></span>

<span data-ttu-id="1f8d3-114">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-114">Right click the *Models* folder.</span></span> <span data-ttu-id="1f8d3-115">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="1f8d3-116">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f8d3-118">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="1f8d3-119">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1f8d3-121">En el Panel de solución, haga clic con el botón derecho en el proyecto **RazorPagesMovie** y seleccione **Agregar** > **Nueva carpeta...** . Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="1f8d3-122">Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo...** .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="1f8d3-123">En el cuadro de diálogo **Nuevo archivo** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1f8d3-124">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1f8d3-125">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1f8d3-126">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1f8d3-127">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1f8d3-128">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="1f8d3-128">Scaffold the movie model</span></span>

<span data-ttu-id="1f8d3-129">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="1f8d3-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1f8d3-130">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-132">Cree una carpeta *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1f8d3-133">Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1f8d3-134">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-134">Name the folder *Movies*</span></span>

<span data-ttu-id="1f8d3-135">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="1f8d3-137">En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="1f8d3-139">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1f8d3-140">En la lista desplegable **Clase de modelo** , seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1f8d3-141">En la fila **Clase de contexto de datos** , seleccione el signo **+** (más) y cambie el nombre generado de RazorPagesMovie. **Models**.RazorPagesMovieContext a RazorPagesMovie. **Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="1f8d3-142">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1f8d3-143">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1f8d3-144">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-144">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

<span data-ttu-id="1f8d3-146">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="1f8d3-148">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="1f8d3-149">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="1f8d3-150">**En Windows** : Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1f8d3-151">**En macOS y Linux** : Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f8d3-153">Cree una carpeta *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1f8d3-154">Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1f8d3-155">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-155">Name the folder *Movies*</span></span>

<span data-ttu-id="1f8d3-156">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...** .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="1f8d3-158">En el cuadro de diálogo **Nuevo scaffolding** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1f8d3-160">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1f8d3-161">En la lista desplegable **Clase de modelo** , seleccione o escriba **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1f8d3-162">En la fila **Clase de contexto de datos** , escriba el nombre de la nueva clase, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="1f8d3-163">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1f8d3-164">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1f8d3-165">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-165">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="1f8d3-167">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="1f8d3-168">Incorporación de herramientas de EF</span><span class="sxs-lookup"><span data-stu-id="1f8d3-168">Add EF tools</span></span>

<span data-ttu-id="1f8d3-169">Ejecute el siguiente comando de la CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="1f8d3-170">El comando anterior incorpora las herramientas de Entity Framework Core para la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="1f8d3-171">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="1f8d3-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-173">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1f8d3-174">*Pages/Movies* : Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1f8d3-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1f8d3-176">Actualizado</span><span class="sxs-lookup"><span data-stu-id="1f8d3-176">Updated</span></span>

* <span data-ttu-id="1f8d3-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-177">*Startup.cs*</span></span>

<span data-ttu-id="1f8d3-178">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f8d3-180">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1f8d3-181">*Pages/Movies* : Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1f8d3-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1f8d3-183">Actualizado</span><span class="sxs-lookup"><span data-stu-id="1f8d3-183">Updated</span></span>

* <span data-ttu-id="1f8d3-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-184">*Startup.cs*</span></span>

<span data-ttu-id="1f8d3-185">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f8d3-187">El proceso de scaffolding crea los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="1f8d3-188">*Pages/Movies* : Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="1f8d3-189">Los archivos creados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1f8d3-190">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="1f8d3-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-192">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1f8d3-193">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-193">Add an initial migration.</span></span>
* <span data-ttu-id="1f8d3-194">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="1f8d3-195">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1f8d3-197">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="1f8d3-200">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="1f8d3-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="1f8d3-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="1f8d3-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="1f8d3-203">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="1f8d3-204">El comando migrations genera código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="1f8d3-205">El esquema se basa en el modelo especificado en `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="1f8d3-206">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="1f8d3-207">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="1f8d3-208">El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="1f8d3-209">En este caso, `update` ejecuta el método `Up` en *Migrations/\<time-stamp>_InitialCreate.cs* , que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1f8d3-211">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="1f8d3-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1f8d3-212">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f8d3-213">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1f8d3-214">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1f8d3-215">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1f8d3-216">La herramienta de scaffolding ha creado un contexto de base de datos de forma automática y lo ha registrado con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1f8d3-217">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1f8d3-218">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1f8d3-219">El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1f8d3-220">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1f8d3-221">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="1f8d3-222">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1f8d3-223">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1f8d3-224">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1f8d3-225">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1f8d3-226">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f8d3-228">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f8d3-230">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1f8d3-231">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1f8d3-231">Test the app</span></span>

* <span data-ttu-id="1f8d3-232">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1f8d3-233">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1f8d3-234">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1f8d3-235">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-235">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1f8d3-237">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1f8d3-238">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1f8d3-239">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1f8d3-240">Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="1f8d3-241">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f8d3-242">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1f8d3-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1f8d3-243">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1f8d3-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f8d3-244">En esta sección, se agregan clases para administrar películas en una [base de datos SQLite](https://www.sqlite.org/index.html) multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="1f8d3-245">Las aplicaciones creadas a partir de una plantilla de ASP.NET Core usan una base de datos SQLite.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="1f8d3-246">Las clases de modelo de la aplicación se usan con [Entity Framework Core (EF Core)](/ef/core) ([Proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite)) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="1f8d3-247">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="1f8d3-248">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1f8d3-249">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1f8d3-250">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="1f8d3-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-252">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="1f8d3-253">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-253">Name the folder *Models*.</span></span>

<span data-ttu-id="1f8d3-254">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-254">Right click the *Models* folder.</span></span> <span data-ttu-id="1f8d3-255">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="1f8d3-256">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f8d3-258">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="1f8d3-259">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1f8d3-261">En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **RazorPagesMovie** y seleccione  **Agregar** > **Carpeta nueva**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="1f8d3-262">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="1f8d3-263">Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="1f8d3-264">En el cuadro de diálogo **Nuevo archivo** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1f8d3-265">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1f8d3-266">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1f8d3-267">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1f8d3-268">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1f8d3-269">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="1f8d3-269">Scaffold the movie model</span></span>

<span data-ttu-id="1f8d3-270">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="1f8d3-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1f8d3-271">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-273">Cree una carpeta *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1f8d3-274">Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1f8d3-275">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-275">Name the folder *Movies*</span></span>

<span data-ttu-id="1f8d3-276">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="1f8d3-278">En el cuadro de diálogo **Agregar scaffold** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="1f8d3-280">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="1f8d3-281">En la lista desplegable **Clase de modelo** , seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1f8d3-282">En la fila **Clase de contexto de datos** , seleccione el signo **+** (más), inicie sesión y acepte el nombre generado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="1f8d3-283">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-283">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arp.png)

<span data-ttu-id="1f8d3-285">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="1f8d3-287">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="1f8d3-288">**En Windows** : Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1f8d3-289">**En macOS y Linux** : Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-290">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f8d3-291">Cree una carpeta *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1f8d3-292">Haga clic con el botón derecho en la carpeta *Páginas* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1f8d3-293">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-293">Name the folder *Movies*</span></span>

<span data-ttu-id="1f8d3-294">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="1f8d3-296">En el cuadro de diálogo **Agregar nuevos scaffolding** , seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1f8d3-298">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="1f8d3-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1f8d3-299">En la lista desplegable **Clase de modelo** , seleccione o escriba **Movie**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="1f8d3-300">En la fila **Clase de contexto de datos** , escriba o seleccione **RazorPagesMovieContext**. Se creará una nueva clase de contexto de la base de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="1f8d3-301">En este caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="1f8d3-302">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-302">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="1f8d3-304">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="1f8d3-305">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1f8d3-306">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="1f8d3-306">Files created</span></span>

* <span data-ttu-id="1f8d3-307">*Pages/Movies* : Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1f8d3-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="1f8d3-309">Archivo actualizado</span><span class="sxs-lookup"><span data-stu-id="1f8d3-309">File updated</span></span>

* <span data-ttu-id="1f8d3-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1f8d3-310">*Startup.cs*</span></span>

<span data-ttu-id="1f8d3-311">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1f8d3-312">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="1f8d3-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f8d3-314">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1f8d3-315">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-315">Add an initial migration.</span></span>
* <span data-ttu-id="1f8d3-316">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="1f8d3-317">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1f8d3-319">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="1f8d3-320">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="1f8d3-321">El esquema se basa en el modelo especificado en `DbContext` (en el archivo *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="1f8d3-322">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="1f8d3-323">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="1f8d3-324">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="1f8d3-325">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="1f8d3-326">El método `Up` crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-328">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="1f8d3-329">Los comandos anteriores generan la advertencia siguiente: *No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie"). This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").* Esta advertencia se puede omitir, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f8d3-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f8d3-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1f8d3-331">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="1f8d3-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1f8d3-332">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f8d3-333">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1f8d3-334">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1f8d3-335">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1f8d3-336">La herramienta de scaffolding ha creado un contexto de base de datos de forma automática y lo ha registrado con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1f8d3-337">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1f8d3-338">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="1f8d3-339">El elemento `RazorPagesMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1f8d3-340">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1f8d3-341">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="1f8d3-342">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1f8d3-343">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1f8d3-344">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1f8d3-345">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1f8d3-346">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1f8d3-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f8d3-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f8d3-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f8d3-348">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f8d3-349">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f8d3-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f8d3-350">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1f8d3-351">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1f8d3-351">Test the app</span></span>

* <span data-ttu-id="1f8d3-352">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1f8d3-353">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="1f8d3-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1f8d3-354">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1f8d3-355">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-355">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1f8d3-357">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1f8d3-358">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1f8d3-359">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1f8d3-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1f8d3-360">Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="1f8d3-361">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="1f8d3-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f8d3-362">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1f8d3-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1f8d3-363">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1f8d3-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
