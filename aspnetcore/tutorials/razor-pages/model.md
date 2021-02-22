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
ms.openlocfilehash: 92bfda330399b43871b3ae0e6b609726f7ad4a91
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564049"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="bf821-104">Parte 2. Adición de un modelo a una aplicación de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf821-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="bf821-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bf821-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="bf821-106">En esta sección, se agregan clases para administrar películas en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="bf821-107">Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="bf821-108">EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="bf821-109">Primero escriba las clases de modelo, y EF Core crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="bf821-110">Las clases de modelo se conocen como clases POCO (del inglés "**P** lain-**O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="bf821-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="bf821-111">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="bf821-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bf821-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="bf821-113">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bf821-115">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto *RazorPagesMovie* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bf821-116">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="bf821-117">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="bf821-118">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="bf821-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="bf821-119">Asigne a la clase el nombre *Película*.</span><span class="sxs-lookup"><span data-stu-id="bf821-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="bf821-120">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-121">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-122">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-123">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-124">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-124">With this attribute:</span></span>

  * <span data-ttu-id="bf821-125">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-126">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bf821-128">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="bf821-129">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf821-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="bf821-130">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-131">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-132">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-133">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-134">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-134">With this attribute:</span></span>

  * <span data-ttu-id="bf821-135">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-136">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="bf821-137">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="bf821-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="bf821-138">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-138">Add a database context class</span></span>

1. <span data-ttu-id="bf821-139">En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="bf821-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="bf821-140">En la carpeta *Data*, agregue un archivo denominado *RazorPagesMovieContext.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="bf821-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="bf821-141">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="bf821-142">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="bf821-143">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="bf821-144">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-144">Add a database connection string</span></span>

<span data-ttu-id="bf821-145">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf821-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="bf821-146">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-146">Register the database context</span></span>

1. <span data-ttu-id="bf821-147">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bf821-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="bf821-148">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bf821-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-149">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bf821-150">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="bf821-151">Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo...**</span><span class="sxs-lookup"><span data-stu-id="bf821-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="bf821-152">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="bf821-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="bf821-153">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="bf821-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="bf821-154">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="bf821-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="bf821-155">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="bf821-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="bf821-156">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-157">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-158">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-159">`[DataType(DataType.Date)]`: el atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-160">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-160">With this attribute:</span></span>

  * <span data-ttu-id="bf821-161">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-162">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="bf821-163">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="bf821-164">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="bf821-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="bf821-165">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="bf821-165">Scaffold the movie model</span></span>

<span data-ttu-id="bf821-166">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="bf821-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="bf821-167">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="bf821-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bf821-169">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="bf821-170">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="bf821-171">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="bf821-172">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="bf821-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/5/sca.png)

1. <span data-ttu-id="bf821-174">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

1. <span data-ttu-id="bf821-176">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="bf821-177">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="bf821-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="bf821-178">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="bf821-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="bf821-179">En el cuadro de diálogo **Agregar contexto de datos**, se genera el nombre de clase `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="bf821-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="bf821-180">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-180">Select **Add**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

<span data-ttu-id="bf821-182">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="bf821-184">Abra un shell de comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*</span><span class="sxs-lookup"><span data-stu-id="bf821-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="bf821-185">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="bf821-186">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="bf821-187">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf821-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="bf821-188">Opción</span><span class="sxs-lookup"><span data-stu-id="bf821-188">Option</span></span>               | <span data-ttu-id="bf821-189">Descripción</span><span class="sxs-lookup"><span data-stu-id="bf821-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="bf821-190">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="bf821-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="bf821-191">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="bf821-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="bf821-192">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="bf821-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="bf821-193">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="bf821-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="bf821-194">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="bf821-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="bf821-195">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="bf821-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="bf821-196">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="bf821-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="bf821-197">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="bf821-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="bf821-198">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="bf821-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="bf821-199">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bf821-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="bf821-200">`IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="bf821-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bf821-202">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="bf821-203">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="bf821-204">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="bf821-205">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**</span><span class="sxs-lookup"><span data-stu-id="bf821-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

1. <span data-ttu-id="bf821-207">En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="bf821-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="bf821-209">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="bf821-210">En la fila **DbContext Class to use:** (Clase DbContext que se usará:), asigne a la clase el nombre `RazorPagesMovie.Data.RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="bf821-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="bf821-211">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-211">Select **Finish**.</span></span>

   ![Imagen de las instrucciones anteriores.](model/_static/5/arpMac.png)

<span data-ttu-id="bf821-213">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="bf821-214">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="bf821-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="bf821-215">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="bf821-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="bf821-216">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bf821-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="bf821-217">`IWebHostEnvironment` se inserta para que la aplicación pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="bf821-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="bf821-218">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="bf821-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-220">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bf821-221">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bf821-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bf821-223">Actualizado</span><span class="sxs-lookup"><span data-stu-id="bf821-223">Updated</span></span>

* <span data-ttu-id="bf821-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-224">*Startup.cs*</span></span>

<span data-ttu-id="bf821-225">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf821-227">El proceso de scaffolding crea los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="bf821-228">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="bf821-229">Los archivos creados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bf821-231">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bf821-232">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bf821-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bf821-234">Actualizado</span><span class="sxs-lookup"><span data-stu-id="bf821-234">Updated</span></span>

* <span data-ttu-id="bf821-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-235">*Startup.cs*</span></span>

<span data-ttu-id="bf821-236">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="bf821-237">Creación del esquema de la base de datos inicial mediante la característica de migración de EF</span><span class="sxs-lookup"><span data-stu-id="bf821-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="bf821-238">La característica de migraciones de Entity Framework Core permite:</span><span class="sxs-lookup"><span data-stu-id="bf821-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="bf821-239">Cree el esquema de la base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-239">Create the initial database schema.</span></span>
* <span data-ttu-id="bf821-240">Actualizar incrementalmente el esquema de base de datos para mantenerlo sincronizado con el modelo de datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bf821-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="bf821-241">Se conservan los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-243">En esta sección, la ventana de la **Consola del Administrador de paquetes** (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="bf821-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="bf821-244">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-244">Add an initial migration.</span></span>
* <span data-ttu-id="bf821-245">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="bf821-246">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="bf821-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menú de PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="bf821-248">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="bf821-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="bf821-250">Ejecute los siguientes comandos de la CLI de .NET:</span><span class="sxs-lookup"><span data-stu-id="bf821-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="bf821-251">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="bf821-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="bf821-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="bf821-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="bf821-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="bf821-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="bf821-254">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="bf821-255">El comando `migrations` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="bf821-256">El esquema se basa en el modelo especificado en `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="bf821-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="bf821-257">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="bf821-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="bf821-258">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="bf821-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="bf821-259">El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado.</span><span class="sxs-lookup"><span data-stu-id="bf821-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="bf821-260">En este caso, `update` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bf821-262">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="bf821-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bf821-263">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bf821-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bf821-264">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bf821-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bf821-265">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="bf821-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bf821-266">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="bf821-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bf821-267">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="bf821-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bf821-268">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf821-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bf821-269">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="bf821-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bf821-270">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="bf821-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="bf821-271">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="bf821-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="bf821-272">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="bf821-273">En el código anterior se crea una propiedad [DbSet/\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="bf821-274">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="bf821-275">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bf821-276">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="bf821-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="bf821-277">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="bf821-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-278">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bf821-279">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="bf821-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="bf821-280">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="bf821-280">Test the app</span></span>

1. <span data-ttu-id="bf821-281">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="bf821-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="bf821-282">Si aparece el siguiente mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="bf821-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="bf821-283">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="bf821-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="bf821-284">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="bf821-284">Test the **Create** link.</span></span>

   ![Página Crear](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="bf821-286">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="bf821-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bf821-287">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="bf821-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="bf821-288">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="bf821-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="bf821-289">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="bf821-290">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bf821-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf821-291">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bf821-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bf821-292">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="bf821-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="bf821-293">En esta sección, se agregan clases para administrar películas.</span><span class="sxs-lookup"><span data-stu-id="bf821-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="bf821-294">Las clases de modelo de la aplicación usan [Entity Framework Core (EF Core)](/ef/core) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="bf821-295">EF Core es un asignador relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="bf821-296">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="bf821-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="bf821-297">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="bf821-298">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bf821-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="bf821-299">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-301">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bf821-302">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-302">Name the folder *Models*.</span></span>

<span data-ttu-id="bf821-303">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="bf821-304">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="bf821-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="bf821-305">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="bf821-305">Name the class **Movie**.</span></span>

<span data-ttu-id="bf821-306">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-307">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-308">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-309">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-310">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-310">With this attribute:</span></span>

  * <span data-ttu-id="bf821-311">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-312">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bf821-313">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf821-315">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="bf821-316">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf821-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="bf821-317">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-318">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-319">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-320">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-321">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-321">With this attribute:</span></span>

  * <span data-ttu-id="bf821-322">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-323">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bf821-324">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="bf821-325">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="bf821-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="bf821-326">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-326">Add a database context class</span></span>

* <span data-ttu-id="bf821-327">En el proyecto *RazorPagesMovie*, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="bf821-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="bf821-328">Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:</span><span class="sxs-lookup"><span data-stu-id="bf821-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="bf821-329">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="bf821-330">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="bf821-331">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="bf821-332">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-332">Add a database connection string</span></span>

<span data-ttu-id="bf821-333">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf821-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="bf821-334">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-334">Register the database context</span></span>

<span data-ttu-id="bf821-335">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bf821-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="bf821-336">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf821-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-337">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bf821-338">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto **RazorPagesMovie** y, después, seleccione **Agregar** > **Nueva carpeta...** Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="bf821-339">Haga clic con el botón derecho en la carpeta *Modelos* y, luego, seleccione **Agregar** > **Nuevo archivo...** .</span><span class="sxs-lookup"><span data-stu-id="bf821-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="bf821-340">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="bf821-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="bf821-341">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="bf821-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="bf821-342">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="bf821-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="bf821-343">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="bf821-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="bf821-344">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-345">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-346">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-347">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-348">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-348">With this attribute:</span></span>

  * <span data-ttu-id="bf821-349">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-350">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="bf821-351">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="bf821-352">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="bf821-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="bf821-353">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="bf821-353">Scaffold the movie model</span></span>

<span data-ttu-id="bf821-354">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="bf821-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="bf821-355">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="bf821-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-357">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bf821-358">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bf821-359">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="bf821-360">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="bf821-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="bf821-362">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="bf821-364">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bf821-365">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="bf821-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="bf821-366">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más) y cambie el nombre generado de RazorPagesMovie.**Models**.RazorPagesMovieContext a RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="bf821-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="bf821-367">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="bf821-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="bf821-368">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="bf821-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="bf821-369">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-369">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/3/arp.png)

<span data-ttu-id="bf821-371">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="bf821-373">Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="bf821-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="bf821-374">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="bf821-375">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="bf821-376">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf821-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="bf821-377">Opción</span><span class="sxs-lookup"><span data-stu-id="bf821-377">Option</span></span>               | <span data-ttu-id="bf821-378">Descripción</span><span class="sxs-lookup"><span data-stu-id="bf821-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="bf821-379">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="bf821-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="bf821-380">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="bf821-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="bf821-381">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="bf821-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="bf821-382">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="bf821-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="bf821-383">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="bf821-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="bf821-384">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="bf821-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="bf821-385">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="bf821-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="bf821-386">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="bf821-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="bf821-387">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="bf821-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="bf821-388">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="bf821-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="bf821-389">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="bf821-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-390">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bf821-391">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bf821-392">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bf821-393">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="bf821-394">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo scaffolding...**</span><span class="sxs-lookup"><span data-stu-id="bf821-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="bf821-396">En el cuadro de diálogo **Nuevo scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="bf821-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="bf821-398">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bf821-399">En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="bf821-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="bf821-400">En la fila **Clase de contexto de datos**, escriba el nombre de la nueva clase, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="bf821-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="bf821-401">[Este cambio](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) no es necesario.</span><span class="sxs-lookup"><span data-stu-id="bf821-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="bf821-402">Crea la clase de contexto de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="bf821-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="bf821-403">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-403">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="bf821-405">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="bf821-406">Incorporación de herramientas de EF</span><span class="sxs-lookup"><span data-stu-id="bf821-406">Add EF tools</span></span>

<span data-ttu-id="bf821-407">Ejecute el siguiente comando de la CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf821-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="bf821-408">El comando anterior incorpora las herramientas de Entity Framework Core para la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf821-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="bf821-409">Para más información, vea [Referencia de herramientas de Entity Framework Core-CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bf821-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="bf821-410">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="bf821-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="bf821-411">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="bf821-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="bf821-412">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="bf821-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="bf821-413">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="bf821-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="bf821-414">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="bf821-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-416">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bf821-417">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bf821-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bf821-419">Actualizado</span><span class="sxs-lookup"><span data-stu-id="bf821-419">Updated</span></span>

* <span data-ttu-id="bf821-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-420">*Startup.cs*</span></span>

<span data-ttu-id="bf821-421">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bf821-423">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bf821-424">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bf821-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bf821-426">Actualizado</span><span class="sxs-lookup"><span data-stu-id="bf821-426">Updated</span></span>

* <span data-ttu-id="bf821-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-427">*Startup.cs*</span></span>

<span data-ttu-id="bf821-428">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf821-430">El proceso de scaffolding crea los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="bf821-431">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="bf821-432">Los archivos creados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="bf821-433">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="bf821-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-435">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="bf821-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="bf821-436">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-436">Add an initial migration.</span></span>
* <span data-ttu-id="bf821-437">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="bf821-438">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="bf821-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="bf821-440">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="bf821-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-441">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="bf821-442">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf821-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="bf821-443">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="bf821-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="bf821-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="bf821-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="bf821-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="bf821-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="bf821-446">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="bf821-447">El comando migrations genera código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="bf821-448">El esquema se basa en el modelo especificado en `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="bf821-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="bf821-449">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="bf821-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="bf821-450">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="bf821-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="bf821-451">El comando `update` ejecuta el método `Up` en las migraciones que no se han aplicado.</span><span class="sxs-lookup"><span data-stu-id="bf821-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="bf821-452">En este caso, `update` ejecuta el método `Up` en *Migrations/\<time-stamp>_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bf821-454">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="bf821-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bf821-455">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bf821-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bf821-456">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bf821-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bf821-457">Estos servicios se proporcionan a los componentes que los necesitan, como páginas Razor, a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="bf821-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="bf821-458">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="bf821-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bf821-459">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="bf821-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bf821-460">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf821-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bf821-461">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="bf821-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bf821-462">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="bf821-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="bf821-463">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="bf821-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="bf821-464">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="bf821-465">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="bf821-466">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="bf821-467">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bf821-468">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="bf821-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="bf821-469">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="bf821-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bf821-471">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="bf821-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="bf821-472">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="bf821-472">Test the app</span></span>

* <span data-ttu-id="bf821-473">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="bf821-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="bf821-474">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="bf821-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="bf821-475">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="bf821-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="bf821-476">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="bf821-476">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="bf821-478">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="bf821-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bf821-479">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="bf821-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="bf821-480">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="bf821-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="bf821-481">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="bf821-482">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bf821-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf821-483">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bf821-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bf821-484">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="bf821-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf821-485">En esta sección, se agregan clases para administrar películas en una [base de datos SQLite](https://www.sqlite.org/index.html) multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bf821-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="bf821-486">Las aplicaciones creadas a partir de una plantilla de ASP.NET Core usan una base de datos SQLite.</span><span class="sxs-lookup"><span data-stu-id="bf821-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="bf821-487">Las clases de modelo de la aplicación se usan con [Entity Framework Core (EF Core)](/ef/core) ([Proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite)) para trabajar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="bf821-488">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="bf821-489">Las clases de modelo se conocen como clases POCO (del inglés "plain-old CLR objects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="bf821-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="bf821-490">Definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="bf821-491">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bf821-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="bf821-492">Agregar un modelo de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-494">Haga clic con el botón derecho en el proyecto **RazorPagesMovie** > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bf821-495">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-495">Name the folder *Models*.</span></span>

<span data-ttu-id="bf821-496">Haga clic con el botón derecho en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="bf821-497">Seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="bf821-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="bf821-498">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="bf821-498">Name the class **Movie**.</span></span>

<span data-ttu-id="bf821-499">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-500">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-501">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-502">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-503">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-503">With this attribute:</span></span>

  * <span data-ttu-id="bf821-504">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-505">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bf821-506">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf821-508">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="bf821-509">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf821-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="bf821-510">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-511">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-512">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-513">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-514">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-514">With this attribute:</span></span>

  * <span data-ttu-id="bf821-515">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-516">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bf821-517">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="bf821-518">Agregue los paquetes NuGet y las herramientas EF.</span><span class="sxs-lookup"><span data-stu-id="bf821-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="bf821-519">Agregar una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-519">Add a database context class</span></span>

<span data-ttu-id="bf821-520">En el proyecto RazorPagesMovie, cree una carpeta denominada *Data*.</span><span class="sxs-lookup"><span data-stu-id="bf821-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="bf821-521">Agregue la clase `RazorPagesMovieContext` siguiente a la carpeta *Data*:</span><span class="sxs-lookup"><span data-stu-id="bf821-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="bf821-522">El código anterior crea una propiedad `DbSet` para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="bf821-523">En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="bf821-524">El código no se compilará hasta que se agreguen las dependencias en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="bf821-525">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-525">Add a database connection string</span></span>

<span data-ttu-id="bf821-526">Agregue una cadena de conexión al archivo *appsettings.json* , tal como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="bf821-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="bf821-527">Adición de los paquetes NuGet necesarios</span><span class="sxs-lookup"><span data-stu-id="bf821-527">Add required NuGet packages</span></span>

<span data-ttu-id="bf821-528">Ejecute el comando siguiente de la CLI de .NET Core para agregar SQLite y CodeGeneration.Design al proyecto:</span><span class="sxs-lookup"><span data-stu-id="bf821-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="bf821-529">El paquete `Microsoft.VisualStudio.Web.CodeGeneration.Design` es necesario para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bf821-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="bf821-530">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="bf821-530">Register the database context</span></span>

<span data-ttu-id="bf821-531">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bf821-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="bf821-532">Registre el contexto de base de datos con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf821-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="bf821-533">Compile el proyecto para comprobar si hay errores.</span><span class="sxs-lookup"><span data-stu-id="bf821-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-534">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bf821-535">En la **ventana de la herramienta de soluciones**, mientras mantiene presionada la tecla Ctrl, haga clic en el proyecto *RazorPagesMovie* y, después, seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="bf821-536">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="bf821-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="bf821-537">Mientras mantiene presionada la tecla Ctrl, haga clic en la carpeta *Models* y, luego, seleccione **Agregar** > **Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="bf821-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="bf821-538">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="bf821-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="bf821-539">Seleccione **General** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="bf821-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="bf821-540">Seleccione **Clase vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="bf821-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="bf821-541">Asigne a la clase el nombre **Películas** y seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="bf821-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="bf821-542">Agregue las propiedades siguientes a la clase `Movie`:</span><span class="sxs-lookup"><span data-stu-id="bf821-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bf821-543">la clase `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="bf821-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="bf821-544">La base de datos requiere el campo `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="bf821-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="bf821-545">`[DataType(DataType.Date)]`: el atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bf821-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bf821-546">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="bf821-546">With this attribute:</span></span>

  * <span data-ttu-id="bf821-547">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="bf821-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bf821-548">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="bf821-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bf821-549">Los elementos [DataAnnotations](xref:System.ComponentModel.DataAnnotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="bf821-550">Compile el proyecto para comprobar que no haya errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="bf821-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="bf821-551">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="bf821-551">Scaffold the movie model</span></span>

<span data-ttu-id="bf821-552">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="bf821-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="bf821-553">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="bf821-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-555">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bf821-556">Haga clic con el botón derecho en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bf821-557">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="bf821-558">Haga clic con el botón derecho en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="bf821-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/sca.png)

<span data-ttu-id="bf821-560">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="bf821-562">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="bf821-563">En la lista desplegable **Clase de modelo**, seleccione **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="bf821-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="bf821-564">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más), inicie sesión y acepte el nombre generado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="bf821-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="bf821-565">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-565">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arp.png)

<span data-ttu-id="bf821-567">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf821-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf821-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="bf821-569">Abra una ventana Comandos en el directorio del proyecto, que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="bf821-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="bf821-570">**En Windows**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="bf821-571">**En macOS y Linux**: Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="bf821-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="bf821-572">En la tabla siguiente se incluyen los detalles de las opciones del generador de código de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf821-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="bf821-573">Opción</span><span class="sxs-lookup"><span data-stu-id="bf821-573">Option</span></span>               | <span data-ttu-id="bf821-574">Descripción</span><span class="sxs-lookup"><span data-stu-id="bf821-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="bf821-575">Nombre del modelo</span><span class="sxs-lookup"><span data-stu-id="bf821-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="bf821-576">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="bf821-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="bf821-577">Usa el diseño predeterminado.</span><span class="sxs-lookup"><span data-stu-id="bf821-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="bf821-578">Ruta de acceso relativa de la carpeta de salida para crear las vistas</span><span class="sxs-lookup"><span data-stu-id="bf821-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="bf821-579">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="bf821-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="bf821-580">Use la opción `-h` para obtener ayuda sobre el comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="bf821-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="bf821-581">Para más información, vea [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="bf821-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf821-582">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bf821-583">Cree una carpeta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="bf821-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bf821-584">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages* > **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="bf821-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bf821-585">Asigne a la carpeta el nombre *Movies*.</span><span class="sxs-lookup"><span data-stu-id="bf821-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="bf821-586">Mientras mantiene presionado Ctrl, haga clic en la carpeta *Pages/Movies* > **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="bf821-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/scaMac.png)

<span data-ttu-id="bf821-588">En el cuadro de diálogo **Agregar nuevos scaffolding**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="bf821-590">Complete el cuadro de diálogo para **agregar instancias de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="bf821-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bf821-591">En la lista desplegable **Clase de modelo**, seleccione o escriba **Movie**.</span><span class="sxs-lookup"><span data-stu-id="bf821-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="bf821-592">En la fila **Clase de contexto de datos**, escriba o seleccione **RazorPagesMovieContext**. Se creará una clase de contexto de la base de datos con el espacio de nombres correcto.</span><span class="sxs-lookup"><span data-stu-id="bf821-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="bf821-593">En este caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="bf821-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="bf821-594">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-594">Select **Add**.</span></span>

![Imagen de las instrucciones anteriores.](model/_static/arpMac.png)

<span data-ttu-id="bf821-596">El archivo *appsettings.json* se actualiza con la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="bf821-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="bf821-597">El proceso de scaffolding crea y actualiza los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="bf821-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="bf821-598">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="bf821-598">Files created</span></span>

* <span data-ttu-id="bf821-599">*Pages/Movies*: Create, Delete, Details, Edit y Index.</span><span class="sxs-lookup"><span data-stu-id="bf821-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bf821-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="bf821-601">Archivo actualizado</span><span class="sxs-lookup"><span data-stu-id="bf821-601">File updated</span></span>

* <span data-ttu-id="bf821-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bf821-602">*Startup.cs*</span></span>

<span data-ttu-id="bf821-603">Los archivos creados y actualizados se explican en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bf821-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="bf821-604">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="bf821-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf821-606">En esta sección, la Consola del administrador de paquetes (PMC) se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="bf821-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="bf821-607">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-607">Add an initial migration.</span></span>
* <span data-ttu-id="bf821-608">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="bf821-609">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="bf821-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menú de PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="bf821-611">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="bf821-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="bf821-612">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="bf821-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="bf821-613">El esquema se basa en el modelo especificado en `DbContext`, en el archivo *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf821-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="bf821-614">El argumento `InitialCreate` se usa para asignar nombre a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="bf821-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="bf821-615">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="bf821-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="bf821-616">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="bf821-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="bf821-617">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf821-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bf821-618">El método `Up` crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-619">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="bf821-620">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf821-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="bf821-621">Los comandos anteriores generan la advertencia siguiente: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="bf821-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="bf821-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="bf821-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="bf821-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." ("No se ha especificado ningún tipo en la columna decimal 'Price' en el tipo de entidad 'Movie'. Esto hará que los valores se trunquen inadvertidamente si no caben según la precisión y escala predeterminados. Especifique expresamente el tipo de columna de SQL Server que tenga cabida para todos los valores usando 'HasColumnType()'.")</span><span class="sxs-lookup"><span data-stu-id="bf821-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="bf821-624">Ignore la advertencia, ya que se tratará en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="bf821-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf821-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf821-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bf821-626">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="bf821-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bf821-627">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bf821-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bf821-628">Los servicios, como el contexto de base de datos de EF Core, se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bf821-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bf821-629">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="bf821-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bf821-630">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="bf821-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bf821-631">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="bf821-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bf821-632">Examine el método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf821-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bf821-633">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="bf821-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="bf821-634">`RazorPagesMovieContext` coordina la funcionalidad de EF Core, como la creación, lectura, actualización y eliminación, del modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="bf821-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="bf821-635">El contexto de datos (`RazorPagesMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="bf821-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="bf821-636">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="bf821-637">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="bf821-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="bf821-638">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="bf821-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="bf821-639">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="bf821-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bf821-640">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="bf821-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="bf821-641">Para el desarrollo local, el [sistema de configuración](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="bf821-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bf821-642">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bf821-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bf821-643">Examine el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="bf821-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="bf821-644">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="bf821-644">Test the app</span></span>

* <span data-ttu-id="bf821-645">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="bf821-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="bf821-646">Si se produce un error:</span><span class="sxs-lookup"><span data-stu-id="bf821-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="bf821-647">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="bf821-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="bf821-648">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="bf821-648">Test the **Create** link.</span></span>

  ![Página Crear](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="bf821-650">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="bf821-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bf821-651">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="bf821-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="bf821-652">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="bf821-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="bf821-653">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="bf821-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="bf821-654">En el tutorial siguiente se explican los archivos creados mediante scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bf821-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf821-655">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bf821-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bf821-656">[Anterior: Introducción](xref:tutorials/razor-pages/razor-pages-start)
> [Siguiente: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="bf821-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
