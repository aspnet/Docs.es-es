---
title: Parte 2. Adición de un modelo
author: rick-anderson
description: Parte 2 de la serie de tutoriales sobre Razor Pages. En esta sección, se agregan clases de modelo.
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485997"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="9a856-104">Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a856-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="9a856-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a856-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9a856-106">En esta sección, se agregan clases para administrar películas en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="9a856-107">Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9a856-108">EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="9a856-109">Primero escriba las clases de modelo, y EF Core crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="9a856-110">Las clases de modelo se conocen como clases POCO (del inglés "**P** lain-**O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9a856-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="9a856-111">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9a856-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9a856-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9a856-113">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9a856-115">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto *RazorPagesMovie* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9a856-116">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="9a856-117">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="9a856-118">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="9a856-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="9a856-119">Asigne a la clase el nombre *Película*.</span><span class="sxs-lookup"><span data-stu-id="9a856-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="9a856-120">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-121">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-122">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-123">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-124">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-124">With this attribute:</span></span>

  * <span data-ttu-id="9a856-125">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-126">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9a856-128">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="9a856-129">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9a856-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9a856-130">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-131">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-132">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-133">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-134">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-134">With this attribute:</span></span>

  * <span data-ttu-id="9a856-135">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-136">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9a856-137">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="9a856-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9a856-138">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-138">Add a database context class</span></span>

1. <span data-ttu-id="9a856-139">En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="9a856-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="9a856-140">En la carpeta *Data*, agregue un archivo denominado *RazorPagesMovieContext.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="9a856-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="9a856-141">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9a856-142">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9a856-143">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9a856-144">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-144">Add a database connection string</span></span>

<span data-ttu-id="9a856-145">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a856-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9a856-146">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-146">Register the database context</span></span>

1. <span data-ttu-id="9a856-147">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a856-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="9a856-148">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a856-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-149">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9a856-150">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="9a856-151">Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo...**</span><span class="sxs-lookup"><span data-stu-id="9a856-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="9a856-152">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="9a856-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="9a856-153">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="9a856-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="9a856-154">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="9a856-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="9a856-155">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="9a856-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="9a856-156">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-157">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-158">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-159">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-160">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-160">With this attribute:</span></span>

  * <span data-ttu-id="9a856-161">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-162">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9a856-163">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9a856-164">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="9a856-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9a856-165">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="9a856-165">Scaffold the movie model</span></span>

<span data-ttu-id="9a856-166">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="9a856-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9a856-167">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="9a856-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9a856-169">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9a856-170">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9a856-171">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9a856-172">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9a856-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/5/sca.png)

1. <span data-ttu-id="9a856-174">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

1. <span data-ttu-id="9a856-176">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9a856-177">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="9a856-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="9a856-178">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="9a856-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="9a856-179">En el cuadro de diálogo **Agregar contexto de datos**, se genera el nombre de clase *RazorPagesMovie.Data.RazorPagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="9a856-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="9a856-180">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-180">Select **Add**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

<span data-ttu-id="9a856-182">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9a856-184">Abra un shell de comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*</span><span class="sxs-lookup"><span data-stu-id="9a856-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9a856-185">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9a856-186">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9a856-187">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a856-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="9a856-188">Opción</span><span class="sxs-lookup"><span data-stu-id="9a856-188">Option</span></span>               | <span data-ttu-id="9a856-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="9a856-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9a856-190">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="9a856-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9a856-191">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="9a856-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9a856-192">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9a856-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9a856-193">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="9a856-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9a856-194">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="9a856-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9a856-195">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="9a856-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9a856-196">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9a856-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9a856-197">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="9a856-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9a856-198">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9a856-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9a856-199">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9a856-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9a856-200">`IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="9a856-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9a856-202">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9a856-203">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9a856-204">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9a856-205">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**</span><span class="sxs-lookup"><span data-stu-id="9a856-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

1. <span data-ttu-id="9a856-207">En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="9a856-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="9a856-209">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9a856-210">En la fila **DbContext Class to use:** , asigne a la clase el nombre *RazorPagesMovie.Data.RazorPagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="9a856-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="9a856-211">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-211">Select **Finish**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/5/arpMac.png)

<span data-ttu-id="9a856-213">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9a856-214">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="9a856-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9a856-215">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9a856-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9a856-216">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9a856-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9a856-217">`IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="9a856-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9a856-218">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="9a856-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-220">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9a856-221">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9a856-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9a856-223">Actualizado</span><span class="sxs-lookup"><span data-stu-id="9a856-223">Updated</span></span>

* <span data-ttu-id="9a856-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-224">*Startup.cs*</span></span>

<span data-ttu-id="9a856-225">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9a856-227">El proceso de scaffolding crea los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9a856-228">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9a856-229">Los archivos creados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9a856-231">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9a856-232">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9a856-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9a856-234">Actualizado</span><span class="sxs-lookup"><span data-stu-id="9a856-234">Updated</span></span>

* <span data-ttu-id="9a856-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-235">*Startup.cs*</span></span>

<span data-ttu-id="9a856-236">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="9a856-237">Creación del esquema de la base de datos inicial mediante la característica de migración de EF</span><span class="sxs-lookup"><span data-stu-id="9a856-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="9a856-238">La característica de migraciones de Entity Framework Core permite:</span><span class="sxs-lookup"><span data-stu-id="9a856-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="9a856-239">Cree el esquema de la base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-239">Create the initial database schema.</span></span>
* <span data-ttu-id="9a856-240">Actualizar incrementalmente el esquema de base de datos para mantenerlo sincronizado con el modelo de datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a856-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="9a856-241">Se conservan los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-243">En esta sección, la ventana de la **Consola del Administrador de paquetes** (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="9a856-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="9a856-244">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-244">Add an initial migration.</span></span>
* <span data-ttu-id="9a856-245">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="9a856-246">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="9a856-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menú de PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="9a856-248">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a856-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="9a856-250">Ejecute los siguientes comandos de la CLI de .NET:</span><span class="sxs-lookup"><span data-stu-id="9a856-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="9a856-251">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9a856-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9a856-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="9a856-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9a856-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="9a856-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9a856-254">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9a856-255">El comando `migrations` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9a856-256">El esquema se basa en el modelo especificado en `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9a856-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9a856-257">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="9a856-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9a856-258">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="9a856-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9a856-259">El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado.</span><span class="sxs-lookup"><span data-stu-id="9a856-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9a856-260">En este caso, `update` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9a856-262">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="9a856-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9a856-263">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9a856-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9a856-264">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a856-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9a856-265">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="9a856-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9a856-266">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="9a856-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9a856-267">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9a856-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9a856-268">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9a856-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a856-269">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="9a856-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9a856-270">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9a856-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9a856-271">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9a856-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9a856-272">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9a856-273">En el código anterior se crea una propiedad [DbSet/\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="9a856-274">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9a856-275">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9a856-276">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9a856-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9a856-277">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9a856-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-278">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9a856-279">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="9a856-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="9a856-280">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9a856-280">Test the app</span></span>

1. <span data-ttu-id="9a856-281">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9a856-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="9a856-282">Si aparece el siguiente mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="9a856-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="9a856-283">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9a856-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="9a856-284">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a856-284">Test the **Create** link.</span></span>

   ![Página Crear](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="9a856-286">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="9a856-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9a856-287">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="9a856-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9a856-288">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9a856-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="9a856-289">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9a856-290">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9a856-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a856-291">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9a856-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9a856-292">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9a856-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9a856-293">En esta sección, se agregan clases para administrar películas.</span><span class="sxs-lookup"><span data-stu-id="9a856-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="9a856-294">Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9a856-295">EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="9a856-296">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9a856-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9a856-297">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9a856-298">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9a856-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9a856-299">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-301">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9a856-302">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-302">Name the folder *Models*.</span></span>

<span data-ttu-id="9a856-303">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="9a856-304">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="9a856-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="9a856-305">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="9a856-305">Name the class **Movie**.</span></span>

<span data-ttu-id="9a856-306">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-307">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-308">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-309">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-310">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-310">With this attribute:</span></span>

  * <span data-ttu-id="9a856-311">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-312">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9a856-313">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9a856-315">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9a856-316">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9a856-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9a856-317">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-318">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-319">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-320">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-321">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-321">With this attribute:</span></span>

  * <span data-ttu-id="9a856-322">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-323">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9a856-324">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9a856-325">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="9a856-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9a856-326">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-326">Add a database context class</span></span>

* <span data-ttu-id="9a856-327">En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="9a856-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="9a856-328">Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:</span><span class="sxs-lookup"><span data-stu-id="9a856-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9a856-329">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9a856-330">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9a856-331">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9a856-332">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-332">Add a database connection string</span></span>

<span data-ttu-id="9a856-333">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a856-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9a856-334">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-334">Register the database context</span></span>

<span data-ttu-id="9a856-335">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a856-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9a856-336">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9a856-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-337">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9a856-338">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto **RazorPagesMovie** y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="9a856-339">Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo...** .</span><span class="sxs-lookup"><span data-stu-id="9a856-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="9a856-340">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="9a856-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9a856-341">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="9a856-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9a856-342">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="9a856-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9a856-343">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="9a856-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9a856-344">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-345">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-346">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-347">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-348">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-348">With this attribute:</span></span>

  * <span data-ttu-id="9a856-349">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-350">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9a856-351">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9a856-352">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="9a856-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9a856-353">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="9a856-353">Scaffold the movie model</span></span>

<span data-ttu-id="9a856-354">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="9a856-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9a856-355">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="9a856-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-357">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9a856-358">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9a856-359">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="9a856-360">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9a856-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="9a856-362">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="9a856-364">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9a856-365">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="9a856-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9a856-366">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más) y cambie el nombre generado de RazorPagesMovie.**Models**.RazorPagesMovieContext a RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9a856-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9a856-367">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="9a856-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9a856-368">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="9a856-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9a856-369">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-369">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

<span data-ttu-id="9a856-371">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9a856-373">Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="9a856-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9a856-374">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9a856-375">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9a856-376">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a856-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9a856-377">Opción</span><span class="sxs-lookup"><span data-stu-id="9a856-377">Option</span></span>               | <span data-ttu-id="9a856-378">Descripción</span><span class="sxs-lookup"><span data-stu-id="9a856-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9a856-379">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="9a856-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9a856-380">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="9a856-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9a856-381">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9a856-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9a856-382">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="9a856-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9a856-383">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="9a856-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9a856-384">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="9a856-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9a856-385">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9a856-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9a856-386">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="9a856-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9a856-387">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9a856-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9a856-388">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="9a856-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9a856-389">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="9a856-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-390">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9a856-391">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9a856-392">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9a856-393">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="9a856-394">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**</span><span class="sxs-lookup"><span data-stu-id="9a856-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="9a856-396">En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="9a856-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9a856-398">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9a856-399">En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="9a856-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9a856-400">En la fila **Clase de contexto de datos**, escriba el nombre de la nueva clase, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9a856-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9a856-401">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="9a856-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9a856-402">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="9a856-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9a856-403">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-403">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="9a856-405">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="9a856-406">Incorporación de herramientas de EF</span><span class="sxs-lookup"><span data-stu-id="9a856-406">Add EF tools</span></span>

<span data-ttu-id="9a856-407">Ejecute el siguiente comando de la CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a856-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="9a856-408">El comando anterior incorpora las herramientas de Entity Framework Core para la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a856-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="9a856-409">Para más información, vea [Referencia de herramientas de Entity Framework Core-CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9a856-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9a856-410">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="9a856-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9a856-411">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="9a856-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9a856-412">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="9a856-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9a856-413">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="9a856-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9a856-414">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="9a856-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-416">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9a856-417">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9a856-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9a856-419">Actualizado</span><span class="sxs-lookup"><span data-stu-id="9a856-419">Updated</span></span>

* <span data-ttu-id="9a856-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-420">*Startup.cs*</span></span>

<span data-ttu-id="9a856-421">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9a856-423">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9a856-424">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9a856-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9a856-426">Actualizado</span><span class="sxs-lookup"><span data-stu-id="9a856-426">Updated</span></span>

* <span data-ttu-id="9a856-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-427">*Startup.cs*</span></span>

<span data-ttu-id="9a856-428">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9a856-430">El proceso de scaffolding crea los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9a856-431">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9a856-432">Los archivos creados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9a856-433">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="9a856-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-435">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="9a856-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9a856-436">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-436">Add an initial migration.</span></span>
* <span data-ttu-id="9a856-437">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="9a856-438">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="9a856-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9a856-440">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a856-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-441">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9a856-442">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a856-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9a856-443">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9a856-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9a856-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="9a856-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9a856-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="9a856-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9a856-446">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9a856-447">El comando migrations genera código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="9a856-448">El esquema se basa en el modelo especificado en `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9a856-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9a856-449">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="9a856-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9a856-450">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="9a856-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9a856-451">El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado.</span><span class="sxs-lookup"><span data-stu-id="9a856-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9a856-452">En este caso, `update` ejecuta el método `Up` en *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9a856-454">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="9a856-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9a856-455">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9a856-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9a856-456">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a856-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9a856-457">Estos servicios se proporcionan a los componentes que los necesitan, como páginas Razor, a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="9a856-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="9a856-458">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="9a856-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9a856-459">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9a856-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9a856-460">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9a856-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a856-461">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="9a856-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9a856-462">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9a856-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9a856-463">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9a856-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9a856-464">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9a856-465">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9a856-466">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9a856-467">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9a856-468">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9a856-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9a856-469">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9a856-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9a856-471">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="9a856-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9a856-472">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9a856-472">Test the app</span></span>

* <span data-ttu-id="9a856-473">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9a856-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9a856-474">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="9a856-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9a856-475">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9a856-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9a856-476">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a856-476">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="9a856-478">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="9a856-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9a856-479">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="9a856-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9a856-480">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9a856-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9a856-481">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9a856-482">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9a856-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a856-483">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9a856-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9a856-484">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9a856-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a856-485">En esta sección, se agregan clases para administrar películas en una [base de datos SQLite](https://www.sqlite.org/index.html) multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="9a856-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="9a856-486">Las aplicaciones creadas a partir de una plantilla de ASP.NET Core usan una base de datos SQLite.</span><span class="sxs-lookup"><span data-stu-id="9a856-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="9a856-487">Las clases de modelo de la aplicación se usan con [Entity Framework Core (EF Core)](/ef/core) ([Proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite)) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="9a856-488">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="9a856-489">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9a856-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9a856-490">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9a856-491">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9a856-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9a856-492">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-494">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9a856-495">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-495">Name the folder *Models*.</span></span>

<span data-ttu-id="9a856-496">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="9a856-497">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="9a856-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="9a856-498">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="9a856-498">Name the class **Movie**.</span></span>

<span data-ttu-id="9a856-499">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-500">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-501">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-502">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-503">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-503">With this attribute:</span></span>

  * <span data-ttu-id="9a856-504">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-505">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9a856-506">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9a856-508">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9a856-509">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9a856-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9a856-510">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-511">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-512">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-513">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-514">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-514">With this attribute:</span></span>

  * <span data-ttu-id="9a856-515">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-516">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9a856-517">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9a856-518">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="9a856-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9a856-519">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-519">Add a database context class</span></span>

<span data-ttu-id="9a856-520">En el proyecto RazorPagesMovie, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="9a856-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="9a856-521">Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:</span><span class="sxs-lookup"><span data-stu-id="9a856-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9a856-522">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9a856-523">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9a856-524">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9a856-525">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-525">Add a database connection string</span></span>

<span data-ttu-id="9a856-526">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="9a856-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="9a856-527">Adición de los paquetes NuGet necesarios</span><span class="sxs-lookup"><span data-stu-id="9a856-527">Add required NuGet packages</span></span>

<span data-ttu-id="9a856-528">Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:</span><span class="sxs-lookup"><span data-stu-id="9a856-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="9a856-529">El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9a856-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9a856-530">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="9a856-530">Register the database context</span></span>

<span data-ttu-id="9a856-531">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a856-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9a856-532">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9a856-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9a856-533">Compile el proyecto para comprobar si hay errores.</span><span class="sxs-lookup"><span data-stu-id="9a856-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-534">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9a856-535">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="9a856-536">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="9a856-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="9a856-537">Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="9a856-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="9a856-538">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="9a856-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9a856-539">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="9a856-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9a856-540">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="9a856-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9a856-541">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="9a856-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9a856-542">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="9a856-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9a856-543">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="9a856-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="9a856-544">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="9a856-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9a856-545">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="9a856-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9a856-546">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="9a856-546">With this attribute:</span></span>

  * <span data-ttu-id="9a856-547">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="9a856-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9a856-548">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="9a856-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9a856-549">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="9a856-550">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="9a856-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9a856-551">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="9a856-551">Scaffold the movie model</span></span>

<span data-ttu-id="9a856-552">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="9a856-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9a856-553">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="9a856-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-555">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9a856-556">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9a856-557">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="9a856-558">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9a856-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="9a856-560">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="9a856-562">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="9a856-563">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="9a856-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9a856-564">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más), inicie sesión y acepte el nombre generado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9a856-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9a856-565">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-565">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arp.png)

<span data-ttu-id="9a856-567">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a856-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a856-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9a856-569">Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="9a856-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9a856-570">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9a856-571">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="9a856-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9a856-572">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a856-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9a856-573">Opción</span><span class="sxs-lookup"><span data-stu-id="9a856-573">Option</span></span>               | <span data-ttu-id="9a856-574">Descripción</span><span class="sxs-lookup"><span data-stu-id="9a856-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9a856-575">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="9a856-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9a856-576">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="9a856-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9a856-577">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9a856-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9a856-578">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="9a856-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9a856-579">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="9a856-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9a856-580">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="9a856-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9a856-581">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9a856-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a856-582">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9a856-583">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9a856-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9a856-584">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="9a856-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9a856-585">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9a856-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="9a856-586">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="9a856-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="9a856-588">En el cuadro de diálogo **Agregar nuevos scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9a856-590">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9a856-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9a856-591">En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9a856-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="9a856-592">En la fila **Clase de contexto de datos**, escriba o seleccione **RazorPagesMovieContext**. Se creará una clase de contexto de la base de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="9a856-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="9a856-593">En este caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9a856-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9a856-594">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-594">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="9a856-596">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="9a856-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="9a856-597">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="9a856-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="9a856-598">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="9a856-598">Files created</span></span>

* <span data-ttu-id="9a856-599">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="9a856-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9a856-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="9a856-601">Archivo actualizado</span><span class="sxs-lookup"><span data-stu-id="9a856-601">File updated</span></span>

* <span data-ttu-id="9a856-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9a856-602">*Startup.cs*</span></span>

<span data-ttu-id="9a856-603">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="9a856-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9a856-604">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="9a856-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9a856-606">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="9a856-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9a856-607">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-607">Add an initial migration.</span></span>
* <span data-ttu-id="9a856-608">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="9a856-609">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="9a856-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9a856-611">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="9a856-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="9a856-612">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="9a856-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9a856-613">El esquema se basa en el modelo especificado en `DbContext`, en el archivo *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="9a856-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="9a856-614">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="9a856-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="9a856-615">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="9a856-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="9a856-616">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="9a856-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="9a856-617">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="9a856-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9a856-618">El método `Up` crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-619">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9a856-620">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a856-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9a856-621">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9a856-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9a856-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="9a856-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9a856-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="9a856-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9a856-624">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="9a856-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a856-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a856-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9a856-626">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="9a856-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9a856-627">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9a856-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9a856-628">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9a856-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9a856-629">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="9a856-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9a856-630">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="9a856-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9a856-631">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9a856-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9a856-632">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9a856-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a856-633">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="9a856-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="9a856-634">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9a856-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9a856-635">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9a856-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9a856-636">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9a856-637">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="9a856-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9a856-638">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9a856-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9a856-639">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="9a856-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9a856-640">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="9a856-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9a856-641">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9a856-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9a856-642">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9a856-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9a856-643">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="9a856-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9a856-644">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="9a856-644">Test the app</span></span>

* <span data-ttu-id="9a856-645">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9a856-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9a856-646">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="9a856-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9a856-647">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9a856-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9a856-648">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="9a856-648">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="9a856-650">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="9a856-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9a856-651">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="9a856-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9a856-652">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9a856-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9a856-653">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="9a856-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9a856-654">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="9a856-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a856-655">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9a856-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9a856-656">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9a856-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
