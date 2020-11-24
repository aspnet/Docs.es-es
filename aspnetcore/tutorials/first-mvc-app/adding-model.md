---
title: Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre ASP.NET Core MVC.
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688454"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="17bd0-103">Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="17bd0-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="17bd0-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="17bd0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="17bd0-105">En esta sección, agregará las clases para administrar películas en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="17bd0-106">Estas clases serán el elemento "**M** odel" de la aplicación **M** VC.</span><span class="sxs-lookup"><span data-stu-id="17bd0-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="17bd0-107">Estas clases se usan con [Entity Framework Core](/ef/core) (EF Core) para trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="17bd0-108">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el código de acceso de datos que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="17bd0-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="17bd0-109">Las clases de modelo que se crean se conocen como clases POCO (del inglés "**p** lain **O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="17bd0-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="17bd0-110">Simplemente definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="17bd0-111">En este tutorial, se escriben primero las clases del modelo y EF Core crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="17bd0-112">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-114">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="17bd0-115">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="17bd0-117">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="17bd0-119">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="17bd0-120">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="17bd0-121">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="17bd0-122">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="17bd0-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="17bd0-123">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="17bd0-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="17bd0-124">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="17bd0-124">With this attribute:</span></span>

* <span data-ttu-id="17bd0-125">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="17bd0-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="17bd0-126">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="17bd0-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="17bd0-127">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="17bd0-128">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="17bd0-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-130">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="17bd0-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="17bd0-132">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="17bd0-133">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="17bd0-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="17bd0-134">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="17bd0-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="17bd0-135">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="17bd0-138">En el menú **Proyecto**, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="17bd0-139">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="17bd0-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="17bd0-140">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="17bd0-142">Se mostrará el cuadro de diálogo **Seleccionar los proyectos**, con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="17bd0-143">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-143">Press the **Ok** button.</span></span>

<span data-ttu-id="17bd0-144">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="17bd0-145">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="17bd0-146">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="17bd0-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="17bd0-147">Ejecute el comando siguiente de la CLI de .NET:</span><span class="sxs-lookup"><span data-stu-id="17bd0-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="17bd0-148">El comando anterior agrega la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="17bd0-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="17bd0-149">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-149">Create a database context class</span></span>

<span data-ttu-id="17bd0-150">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="17bd0-151">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="17bd0-152">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-152">Create a *Data* folder.</span></span>

<span data-ttu-id="17bd0-153">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="17bd0-154">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="17bd0-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="17bd0-155">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="17bd0-156">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="17bd0-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="17bd0-157">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-157">Register the database context</span></span>

<span data-ttu-id="17bd0-158">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17bd0-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="17bd0-159">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="17bd0-160">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="17bd0-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="17bd0-161">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="17bd0-162">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17bd0-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="17bd0-163">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="17bd0-164">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="17bd0-167">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="17bd0-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="17bd0-168">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="17bd0-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="17bd0-169">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-169">Add a database connection string</span></span>

<span data-ttu-id="17bd0-170">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="17bd0-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-172">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="17bd0-173">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="17bd0-174">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="17bd0-174">Scaffold movie pages</span></span>

<span data-ttu-id="17bd0-175">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="17bd0-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-177">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="17bd0-179">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="17bd0-181">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="17bd0-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="17bd0-182">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="17bd0-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="17bd0-183">**Clase de contexto de datos**: *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="17bd0-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc5.png)

* <span data-ttu-id="17bd0-185">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="17bd0-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="17bd0-186">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="17bd0-187">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-187">Select **Add**</span></span>

<span data-ttu-id="17bd0-188">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="17bd0-188">Visual Studio creates:</span></span>

* <span data-ttu-id="17bd0-189">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="17bd0-190">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="17bd0-191">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="17bd0-193">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="17bd0-194">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="17bd0-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="17bd0-195">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="17bd0-197">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="17bd0-198">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="17bd0-199">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="17bd0-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="17bd0-200">Si ejecuta la aplicación y hace clic en el vínculo **Movie App**, obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="17bd0-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="17bd0-201">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="17bd0-201">Initial migration</span></span>

<span data-ttu-id="17bd0-202">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="17bd0-203">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-205">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="17bd0-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="17bd0-206">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="17bd0-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="17bd0-207">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="17bd0-208">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="17bd0-209">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="17bd0-210">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="17bd0-211">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="17bd0-212">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="17bd0-213">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="17bd0-214">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="17bd0-215">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="17bd0-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="17bd0-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="17bd0-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="17bd0-217">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="17bd0-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="17bd0-218">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-219">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="17bd0-220">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="17bd0-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="17bd0-221">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="17bd0-222">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="17bd0-223">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="17bd0-224">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="17bd0-225">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="17bd0-226">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="17bd0-227">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="17bd0-228">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="17bd0-228">The InitialCreate class</span></span>

<span data-ttu-id="17bd0-229">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="17bd0-230">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="17bd0-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="17bd0-231">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="17bd0-232">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="17bd0-232">Test the app</span></span>

* <span data-ttu-id="17bd0-233">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="17bd0-234">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="17bd0-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-236">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="17bd0-237">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="17bd0-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="17bd0-238">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-238">Test the **Create** page.</span></span> <span data-ttu-id="17bd0-239">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="17bd0-240">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="17bd0-241">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="17bd0-242">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="17bd0-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="17bd0-243">Pruebe las páginas **Edit**, **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="17bd0-244">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="17bd0-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-246">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="17bd0-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="17bd0-247">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="17bd0-248">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="17bd0-250">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="17bd0-251">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="17bd0-252">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="17bd0-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="17bd0-253">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="17bd0-254">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="17bd0-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="17bd0-255">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="17bd0-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="17bd0-256">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="17bd0-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="17bd0-257">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="17bd0-258">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="17bd0-259">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="17bd0-260">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="17bd0-261">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="17bd0-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="17bd0-262">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="17bd0-263">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="17bd0-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="17bd0-264">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="17bd0-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="17bd0-265">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="17bd0-266">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="17bd0-267">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="17bd0-268">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="17bd0-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="17bd0-269">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="17bd0-270">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="17bd0-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="17bd0-271">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="17bd0-272">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="17bd0-273">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="17bd0-274">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="17bd0-275">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="17bd0-276">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="17bd0-277">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="17bd0-278">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="17bd0-279">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="17bd0-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="17bd0-280">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="17bd0-281">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="17bd0-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="17bd0-282">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="17bd0-283">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="17bd0-284">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="17bd0-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="17bd0-285">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="17bd0-286">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17bd0-287">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17bd0-287">Additional resources</span></span>

* [<span data-ttu-id="17bd0-288">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="17bd0-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="17bd0-289">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="17bd0-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="17bd0-290">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="17bd0-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="17bd0-291">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-293">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="17bd0-294">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="17bd0-296">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-297">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="17bd0-298">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="17bd0-299">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="17bd0-300">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="17bd0-301">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="17bd0-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="17bd0-302">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="17bd0-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="17bd0-303">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="17bd0-303">With this attribute:</span></span>

* <span data-ttu-id="17bd0-304">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="17bd0-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="17bd0-305">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="17bd0-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="17bd0-306">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="17bd0-307">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="17bd0-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-309">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="17bd0-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="17bd0-311">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="17bd0-312">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="17bd0-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="17bd0-313">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="17bd0-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="17bd0-314">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-316">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="17bd0-317">En el menú **Proyecto**, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="17bd0-318">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="17bd0-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="17bd0-319">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="17bd0-321">Se mostrará el cuadro de diálogo **Seleccionar los proyectos**, con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="17bd0-322">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-322">Press the **Ok** button.</span></span>

<span data-ttu-id="17bd0-323">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="17bd0-324">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="17bd0-325">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="17bd0-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="17bd0-326">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-326">Create a database context class</span></span>

<span data-ttu-id="17bd0-327">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="17bd0-328">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="17bd0-329">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-329">Create a *Data* folder.</span></span>

<span data-ttu-id="17bd0-330">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="17bd0-331">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="17bd0-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="17bd0-332">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="17bd0-333">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="17bd0-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="17bd0-334">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-334">Register the database context</span></span>

<span data-ttu-id="17bd0-335">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17bd0-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="17bd0-336">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="17bd0-337">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="17bd0-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="17bd0-338">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="17bd0-339">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17bd0-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="17bd0-340">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="17bd0-341">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-343">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="17bd0-344">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="17bd0-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="17bd0-345">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="17bd0-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="17bd0-346">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-346">Add a database connection string</span></span>

<span data-ttu-id="17bd0-347">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="17bd0-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-349">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="17bd0-350">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="17bd0-351">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="17bd0-351">Scaffold movie pages</span></span>

<span data-ttu-id="17bd0-352">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="17bd0-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-354">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="17bd0-356">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="17bd0-358">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="17bd0-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="17bd0-359">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="17bd0-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="17bd0-360">**Clase de contexto de datos**: *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="17bd0-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc3.png)

* <span data-ttu-id="17bd0-362">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="17bd0-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="17bd0-363">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="17bd0-364">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-364">Select **Add**</span></span>

<span data-ttu-id="17bd0-365">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="17bd0-365">Visual Studio creates:</span></span>

* <span data-ttu-id="17bd0-366">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="17bd0-367">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="17bd0-368">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="17bd0-370">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="17bd0-371">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="17bd0-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="17bd0-372">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-373">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="17bd0-374">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="17bd0-375">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="17bd0-376">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="17bd0-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="17bd0-377">Si ejecuta la aplicación y hace clic en el vínculo **Movie App**, obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="17bd0-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="17bd0-378">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="17bd0-378">Initial migration</span></span>

<span data-ttu-id="17bd0-379">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="17bd0-380">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-382">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="17bd0-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="17bd0-383">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="17bd0-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="17bd0-384">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="17bd0-385">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="17bd0-386">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="17bd0-387">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="17bd0-388">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="17bd0-389">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="17bd0-390">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="17bd0-391">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="17bd0-392">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="17bd0-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="17bd0-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="17bd0-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="17bd0-394">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="17bd0-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="17bd0-395">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-396">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="17bd0-397">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="17bd0-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="17bd0-398">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="17bd0-399">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="17bd0-400">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="17bd0-401">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="17bd0-402">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="17bd0-403">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="17bd0-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="17bd0-404">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="17bd0-405">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="17bd0-405">The InitialCreate class</span></span>

<span data-ttu-id="17bd0-406">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="17bd0-407">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="17bd0-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="17bd0-408">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="17bd0-409">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="17bd0-409">Test the app</span></span>

* <span data-ttu-id="17bd0-410">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="17bd0-411">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="17bd0-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-413">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="17bd0-414">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="17bd0-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="17bd0-415">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-415">Test the **Create** page.</span></span> <span data-ttu-id="17bd0-416">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="17bd0-417">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="17bd0-418">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="17bd0-419">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="17bd0-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="17bd0-420">Pruebe las páginas **Edit**, **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="17bd0-421">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="17bd0-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-423">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="17bd0-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="17bd0-424">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="17bd0-425">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-426">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="17bd0-427">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="17bd0-428">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="17bd0-429">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="17bd0-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="17bd0-430">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17bd0-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="17bd0-431">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="17bd0-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="17bd0-432">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="17bd0-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="17bd0-433">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="17bd0-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="17bd0-434">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="17bd0-435">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="17bd0-436">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="17bd0-437">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="17bd0-438">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="17bd0-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="17bd0-439">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="17bd0-440">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="17bd0-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="17bd0-441">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="17bd0-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="17bd0-442">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="17bd0-443">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="17bd0-444">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="17bd0-445">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="17bd0-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="17bd0-446">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="17bd0-447">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="17bd0-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="17bd0-448">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="17bd0-449">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="17bd0-450">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="17bd0-451">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="17bd0-452">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="17bd0-453">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="17bd0-454">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="17bd0-455">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="17bd0-456">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="17bd0-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="17bd0-457">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="17bd0-458">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="17bd0-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="17bd0-459">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="17bd0-460">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="17bd0-461">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="17bd0-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="17bd0-462">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="17bd0-463">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17bd0-464">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17bd0-464">Additional resources</span></span>

* [<span data-ttu-id="17bd0-465">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="17bd0-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="17bd0-466">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="17bd0-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="17bd0-467">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="17bd0-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="17bd0-468">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="17bd0-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-470">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="17bd0-471">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-472">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="17bd0-473">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="17bd0-474">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="17bd0-474">Scaffold the movie model</span></span>

<span data-ttu-id="17bd0-475">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="17bd0-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="17bd0-476">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="17bd0-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-478">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="17bd0-480">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="17bd0-482">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="17bd0-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="17bd0-483">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="17bd0-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="17bd0-484">**Clase de contexto de datos**: seleccione el icono **+** y agregue el valor predeterminado **MvcMovie.Models.MvcMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adición de contexto de datos](adding-model/_static/dc.png)

* <span data-ttu-id="17bd0-486">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="17bd0-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="17bd0-487">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="17bd0-488">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-488">Select **Add**</span></span>

![Cuadro de diálogo Agregar controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="17bd0-490">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="17bd0-490">Visual Studio creates:</span></span>

* <span data-ttu-id="17bd0-491">Una [clase de contexto de base de datos](xref:data/ef-mvc/intro#create-the-database-context) de Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="17bd0-492">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="17bd0-493">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="17bd0-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="17bd0-494">La creación automática del contexto de base de datos y de vistas y métodos de acción [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (crear, leer, actualizar y eliminar) se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="17bd0-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="17bd0-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17bd0-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="17bd0-496">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="17bd0-497">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="17bd0-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="17bd0-498">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="17bd0-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="17bd0-499">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-500">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="17bd0-501">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="17bd0-502">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="17bd0-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="17bd0-503">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17bd0-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="17bd0-504">Si ejecuta la aplicación y hace clic en el vínculo **Mvc Movie**, aparece un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-506">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="17bd0-507">Debe crear la base de datos y usar para ello la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core.</span><span class="sxs-lookup"><span data-stu-id="17bd0-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="17bd0-508">Las migraciones permiten crear una base de datos que coincide con el modelo de datos y actualizan el esquema de base de datos cuando cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="17bd0-509">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="17bd0-509">Initial migration</span></span>

<span data-ttu-id="17bd0-510">En esta sección, se completan las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="17bd0-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="17bd0-511">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-511">Add an initial migration.</span></span>
* <span data-ttu-id="17bd0-512">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="17bd0-514">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="17bd0-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="17bd0-516">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="17bd0-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="17bd0-517">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="17bd0-518">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="17bd0-519">El argumento `Initial` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="17bd0-520">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="17bd0-521">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="17bd0-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="17bd0-522">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/{time-stamp}_InitialCreate.cs*, con lo que se crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-523">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="17bd0-524">El comando `ef migrations add InitialCreate` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="17bd0-525">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="17bd0-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="17bd0-526">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="17bd0-527">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="17bd0-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="17bd0-528">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="17bd0-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="17bd0-529">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17bd0-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="17bd0-530">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="17bd0-531">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="17bd0-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="17bd0-532">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17bd0-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bd0-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bd0-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bd0-534">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17bd0-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="17bd0-535">Consulte el siguiente método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="17bd0-536">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="17bd0-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="17bd0-537">El elemento `MvcMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="17bd0-538">El contexto de datos (`MvcMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="17bd0-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="17bd0-539">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="17bd0-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="17bd0-540">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="17bd0-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="17bd0-541">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="17bd0-542">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="17bd0-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="17bd0-543">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="17bd0-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="17bd0-544">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="17bd0-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17bd0-545">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17bd0-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="17bd0-546">Ha creado un contexto de base de datos y lo ha registrado con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17bd0-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="17bd0-547">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="17bd0-547">Test the app</span></span>

* <span data-ttu-id="17bd0-548">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="17bd0-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="17bd0-549">Si se produce una excepción de base de datos similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="17bd0-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="17bd0-550">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="17bd0-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="17bd0-551">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-551">Test the **Create** link.</span></span> <span data-ttu-id="17bd0-552">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="17bd0-553">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="17bd0-554">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="17bd0-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="17bd0-555">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="17bd0-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="17bd0-556">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="17bd0-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="17bd0-557">Examine la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="17bd0-558">En el código resaltado anterior se muestra cómo se agrega el contexto de base de datos de películas al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="17bd0-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="17bd0-559">`services.AddDbContext<MvcMovieContext>(options =>` especifica la base de datos que se usará y la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="17bd0-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="17bd0-560">`=>` es un [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="17bd0-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="17bd0-561">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="17bd0-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="17bd0-562">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="17bd0-563">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="17bd0-564">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="17bd0-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="17bd0-565">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="17bd0-566">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="17bd0-567">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="17bd0-568">Este enfoque fuertemente tipado permite una mejor comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="17bd0-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="17bd0-569">El mecanismo de scaffolding usó este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas cuando creó los métodos y las vistas.</span><span class="sxs-lookup"><span data-stu-id="17bd0-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="17bd0-570">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="17bd0-571">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="17bd0-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="17bd0-572">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="17bd0-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="17bd0-573">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="17bd0-574">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="17bd0-575">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="17bd0-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="17bd0-576">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="17bd0-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="17bd0-577">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="17bd0-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="17bd0-578">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="17bd0-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="17bd0-579">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="17bd0-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="17bd0-580">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="17bd0-581">Mediante la inclusión de una instrucción `@model` en la parte superior del archivo de vista, puede especificar el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="17bd0-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="17bd0-582">Al crear el controlador de película, se incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="17bd0-583">Esta directiva `@model` permite acceder a la película que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="17bd0-584">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="17bd0-585">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="17bd0-586">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="17bd0-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="17bd0-587">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="17bd0-588">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="17bd0-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="17bd0-589">Cuando se creó el controlador movies, el scaffolding incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="17bd0-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="17bd0-590">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="17bd0-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="17bd0-591">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="17bd0-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="17bd0-592">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17bd0-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="17bd0-593">Entre otras ventajas, esto implica que se obtiene una comprobación del código en tiempo de compilación:</span><span class="sxs-lookup"><span data-stu-id="17bd0-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17bd0-594">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17bd0-594">Additional resources</span></span>

* [<span data-ttu-id="17bd0-595">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="17bd0-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="17bd0-596">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="17bd0-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="17bd0-597">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con una base de datos](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="17bd0-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
