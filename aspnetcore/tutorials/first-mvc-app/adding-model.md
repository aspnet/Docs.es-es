---
title: Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422750"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4a234-103">Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4a234-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4a234-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4a234-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4a234-105">En esta sección, agregará las clases para administrar películas en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="4a234-106">Estas clases serán el elemento " **M** odel" de la aplicación **M** VC.</span><span class="sxs-lookup"><span data-stu-id="4a234-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="4a234-107">Estas clases se usan con [Entity Framework Core](/ef/core) (EF Core) para trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4a234-108">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el código de acceso de datos que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="4a234-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="4a234-109">Las clases de modelo que se crean se conocen como clases POCO (del inglés " **p** lain **O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4a234-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4a234-110">Simplemente definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="4a234-111">En este tutorial, se escriben primero las clases del modelo y EF Core crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="4a234-112">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-114">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a234-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="4a234-115">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="4a234-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4a234-117">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a234-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a234-119">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="4a234-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="4a234-120">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="4a234-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="4a234-121">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="4a234-122">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4a234-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="4a234-123">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="4a234-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="4a234-124">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="4a234-124">With this attribute:</span></span>

* <span data-ttu-id="4a234-125">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="4a234-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="4a234-126">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="4a234-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="4a234-127">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="4a234-128">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="4a234-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-130">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="4a234-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4a234-132">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="4a234-133">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4a234-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="4a234-134">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="4a234-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="4a234-135">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a234-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a234-138">En el menú **Proyecto** , seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4a234-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="4a234-139">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4a234-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="4a234-140">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="4a234-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="4a234-142">Se mostrará el cuadro de diálogo **Seleccionar los proyectos** , con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="4a234-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="4a234-143">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-143">Press the **Ok** button.</span></span>

<span data-ttu-id="4a234-144">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="4a234-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="4a234-145">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="4a234-146">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a234-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="4a234-147">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-147">Create a database context class</span></span>

<span data-ttu-id="4a234-148">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="4a234-149">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="4a234-150">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="4a234-150">Create a *Data* folder.</span></span>

<span data-ttu-id="4a234-151">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="4a234-152">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="4a234-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4a234-153">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4a234-154">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="4a234-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="4a234-155">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-155">Register the database context</span></span>

<span data-ttu-id="4a234-156">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a234-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4a234-157">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a234-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="4a234-158">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="4a234-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4a234-159">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a234-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="4a234-160">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="4a234-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="4a234-161">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="4a234-162">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4a234-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="4a234-165">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="4a234-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4a234-166">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="4a234-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="4a234-167">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-167">Add a database connection string</span></span>

<span data-ttu-id="4a234-168">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="4a234-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-170">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="4a234-171">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="4a234-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="4a234-172">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="4a234-172">Scaffold movie pages</span></span>

<span data-ttu-id="4a234-173">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="4a234-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-175">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="4a234-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="4a234-177">En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="4a234-179">Rellene el cuadro de diálogo **Agregar controlador** :</span><span class="sxs-lookup"><span data-stu-id="4a234-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="4a234-180">**Clase de modelo** : *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="4a234-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="4a234-181">**Clase de contexto de datos** : *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="4a234-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc5.png)

* <span data-ttu-id="4a234-183">**Vistas** : conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="4a234-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="4a234-184">**Nombre del controlador** : conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="4a234-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="4a234-185">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-185">Select **Add**</span></span>

<span data-ttu-id="4a234-186">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="4a234-186">Visual Studio creates:</span></span>

* <span data-ttu-id="4a234-187">Un controlador de películas ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="4a234-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="4a234-188">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="4a234-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="4a234-189">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="4a234-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="4a234-191">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="4a234-192">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="4a234-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="4a234-193">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a234-195">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="4a234-196">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="4a234-197">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="4a234-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="4a234-198">Si ejecuta la aplicación y hace clic en el vínculo **Movie App** , obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="4a234-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="4a234-199">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="4a234-199">Initial migration</span></span>

<span data-ttu-id="4a234-200">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="4a234-201">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-203">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="4a234-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="4a234-204">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4a234-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="4a234-205">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a234-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="4a234-206">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="4a234-207">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="4a234-208">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="4a234-209">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="4a234-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="4a234-210">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="4a234-211">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="4a234-212">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="4a234-213">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="4a234-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4a234-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4a234-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4a234-215">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="4a234-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="4a234-216">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-217">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="4a234-218">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="4a234-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="4a234-219">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a234-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="4a234-220">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="4a234-221">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="4a234-222">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="4a234-223">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="4a234-224">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="4a234-225">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="4a234-226">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="4a234-226">The InitialCreate class</span></span>

<span data-ttu-id="4a234-227">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="4a234-228">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4a234-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="4a234-229">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="4a234-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="4a234-230">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4a234-230">Test the app</span></span>

* <span data-ttu-id="4a234-231">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="4a234-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="4a234-232">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a234-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-234">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="4a234-235">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="4a234-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="4a234-236">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="4a234-236">Test the **Create** page.</span></span> <span data-ttu-id="4a234-237">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="4a234-238">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="4a234-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4a234-239">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="4a234-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4a234-240">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="4a234-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4a234-241">Pruebe las páginas **Edit** , **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="4a234-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="4a234-242">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="4a234-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-244">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="4a234-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="4a234-245">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="4a234-246">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="4a234-248">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="4a234-249">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="4a234-250">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="4a234-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="4a234-251">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="4a234-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="4a234-252">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="4a234-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="4a234-253">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="4a234-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="4a234-254">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="4a234-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="4a234-255">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="4a234-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="4a234-256">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="4a234-257">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="4a234-258">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="4a234-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="4a234-259">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="4a234-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="4a234-260">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="4a234-261">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="4a234-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="4a234-262">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="4a234-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="4a234-263">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="4a234-264">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="4a234-265">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="4a234-266">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="4a234-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="4a234-267">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="4a234-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="4a234-268">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="4a234-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="4a234-269">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="4a234-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="4a234-270">Examine el contenido del archivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="4a234-271">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="4a234-272">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="4a234-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="4a234-273">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="4a234-274">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="4a234-275">Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="4a234-276">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="4a234-277">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="4a234-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="4a234-278">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="4a234-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="4a234-279">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="4a234-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="4a234-280">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="4a234-281">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="4a234-282">Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="4a234-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="4a234-283">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="4a234-284">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="4a234-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a234-285">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a234-285">Additional resources</span></span>

* [<span data-ttu-id="4a234-286">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="4a234-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="4a234-287">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="4a234-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="4a234-288">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="4a234-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="4a234-289">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-291">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a234-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="4a234-292">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="4a234-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4a234-294">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a234-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a234-296">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="4a234-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="4a234-297">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="4a234-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="4a234-298">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="4a234-299">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4a234-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="4a234-300">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="4a234-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="4a234-301">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="4a234-301">With this attribute:</span></span>

* <span data-ttu-id="4a234-302">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="4a234-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="4a234-303">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="4a234-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="4a234-304">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="4a234-305">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="4a234-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-307">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="4a234-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4a234-309">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="4a234-310">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4a234-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="4a234-311">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="4a234-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="4a234-312">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a234-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-314">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a234-315">En el menú **Proyecto** , seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4a234-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="4a234-316">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4a234-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="4a234-317">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="4a234-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="4a234-319">Se mostrará el cuadro de diálogo **Seleccionar los proyectos** , con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="4a234-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="4a234-320">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-320">Press the **Ok** button.</span></span>

<span data-ttu-id="4a234-321">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="4a234-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="4a234-322">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="4a234-323">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a234-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="4a234-324">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-324">Create a database context class</span></span>

<span data-ttu-id="4a234-325">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="4a234-326">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="4a234-327">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="4a234-327">Create a *Data* folder.</span></span>

<span data-ttu-id="4a234-328">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="4a234-329">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="4a234-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4a234-330">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4a234-331">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="4a234-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="4a234-332">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-332">Register the database context</span></span>

<span data-ttu-id="4a234-333">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a234-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4a234-334">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a234-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="4a234-335">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="4a234-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4a234-336">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a234-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="4a234-337">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="4a234-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="4a234-338">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="4a234-339">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4a234-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-341">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="4a234-342">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="4a234-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4a234-343">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="4a234-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="4a234-344">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-344">Add a database connection string</span></span>

<span data-ttu-id="4a234-345">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="4a234-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-347">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="4a234-348">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="4a234-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="4a234-349">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="4a234-349">Scaffold movie pages</span></span>

<span data-ttu-id="4a234-350">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="4a234-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-352">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="4a234-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="4a234-354">En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="4a234-356">Rellene el cuadro de diálogo **Agregar controlador** :</span><span class="sxs-lookup"><span data-stu-id="4a234-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="4a234-357">**Clase de modelo** : *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="4a234-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="4a234-358">**Clase de contexto de datos** : *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="4a234-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc3.png)

* <span data-ttu-id="4a234-360">**Vistas** : conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="4a234-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="4a234-361">**Nombre del controlador** : conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="4a234-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="4a234-362">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-362">Select **Add**</span></span>

<span data-ttu-id="4a234-363">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="4a234-363">Visual Studio creates:</span></span>

* <span data-ttu-id="4a234-364">Un controlador de películas ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="4a234-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="4a234-365">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="4a234-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="4a234-366">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="4a234-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="4a234-368">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="4a234-369">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="4a234-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="4a234-370">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-371">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a234-372">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="4a234-373">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="4a234-374">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="4a234-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="4a234-375">Si ejecuta la aplicación y hace clic en el vínculo **Movie App** , obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="4a234-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="4a234-376">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="4a234-376">Initial migration</span></span>

<span data-ttu-id="4a234-377">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="4a234-378">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-380">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="4a234-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="4a234-381">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4a234-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="4a234-382">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a234-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="4a234-383">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="4a234-384">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="4a234-385">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="4a234-386">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="4a234-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="4a234-387">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="4a234-388">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="4a234-389">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="4a234-390">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="4a234-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4a234-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4a234-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4a234-392">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="4a234-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="4a234-393">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-394">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="4a234-395">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="4a234-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="4a234-396">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a234-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="4a234-397">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="4a234-398">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="4a234-399">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="4a234-400">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="4a234-401">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="4a234-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="4a234-402">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs* , que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="4a234-403">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="4a234-403">The InitialCreate class</span></span>

<span data-ttu-id="4a234-404">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="4a234-405">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4a234-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="4a234-406">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="4a234-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="4a234-407">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4a234-407">Test the app</span></span>

* <span data-ttu-id="4a234-408">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="4a234-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="4a234-409">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a234-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-411">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="4a234-412">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="4a234-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="4a234-413">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="4a234-413">Test the **Create** page.</span></span> <span data-ttu-id="4a234-414">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="4a234-415">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="4a234-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4a234-416">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="4a234-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4a234-417">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="4a234-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4a234-418">Pruebe las páginas **Edit** , **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="4a234-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="4a234-419">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="4a234-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-421">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="4a234-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="4a234-422">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="4a234-423">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-424">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="4a234-425">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="4a234-426">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="4a234-427">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="4a234-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="4a234-428">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="4a234-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="4a234-429">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="4a234-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="4a234-430">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="4a234-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="4a234-431">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="4a234-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="4a234-432">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="4a234-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="4a234-433">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="4a234-434">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="4a234-435">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="4a234-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="4a234-436">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="4a234-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="4a234-437">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="4a234-438">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="4a234-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="4a234-439">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="4a234-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="4a234-440">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="4a234-441">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="4a234-442">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="4a234-443">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="4a234-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="4a234-444">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="4a234-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="4a234-445">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="4a234-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="4a234-446">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="4a234-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="4a234-447">Examine el contenido del archivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="4a234-448">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="4a234-449">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="4a234-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="4a234-450">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="4a234-451">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="4a234-452">Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="4a234-453">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="4a234-454">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="4a234-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="4a234-455">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="4a234-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="4a234-456">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="4a234-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="4a234-457">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="4a234-458">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="4a234-459">Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="4a234-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="4a234-460">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="4a234-461">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="4a234-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a234-462">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a234-462">Additional resources</span></span>

* [<span data-ttu-id="4a234-463">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="4a234-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="4a234-464">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="4a234-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="4a234-465">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="4a234-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="4a234-466">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="4a234-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-468">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a234-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="4a234-469">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="4a234-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4a234-471">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="4a234-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4a234-472">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="4a234-472">Scaffold the movie model</span></span>

<span data-ttu-id="4a234-473">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="4a234-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4a234-474">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="4a234-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-476">En el **Explorador de soluciones** , haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="4a234-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="4a234-478">En el cuadro de diálogo **Agregar scaffold** , seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="4a234-480">Rellene el cuadro de diálogo **Agregar controlador** :</span><span class="sxs-lookup"><span data-stu-id="4a234-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="4a234-481">**Clase de modelo** : *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="4a234-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="4a234-482">**Clase de contexto de datos** : seleccione el icono **+** y agregue el valor predeterminado **MvcMovie.Models.MvcMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="4a234-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adición de contexto de datos](adding-model/_static/dc.png)

* <span data-ttu-id="4a234-484">**Vistas** : conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="4a234-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="4a234-485">**Nombre del controlador** : conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="4a234-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="4a234-486">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-486">Select **Add**</span></span>

![Cuadro de diálogo Agregar controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="4a234-488">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="4a234-488">Visual Studio creates:</span></span>

* <span data-ttu-id="4a234-489">Una [clase de contexto de base de datos](xref:data/ef-mvc/intro#create-the-database-context) de Entity Framework Core ( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="4a234-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="4a234-490">Un controlador de películas ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="4a234-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="4a234-491">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="4a234-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="4a234-492">La creación automática del contexto de base de datos y de vistas y métodos de acción [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (crear, leer, actualizar y eliminar) se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="4a234-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a234-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a234-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4a234-494">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="4a234-495">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="4a234-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4a234-496">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="4a234-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="4a234-497">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a234-498">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a234-499">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs* , *Startup.cs* y *.csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="4a234-500">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="4a234-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4a234-501">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4a234-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="4a234-502">Si ejecuta la aplicación y hace clic en el vínculo **Mvc Movie** , aparece un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-504">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="4a234-505">Debe crear la base de datos y usar para ello la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core.</span><span class="sxs-lookup"><span data-stu-id="4a234-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="4a234-506">Las migraciones permiten crear una base de datos que coincide con el modelo de datos y actualizan el esquema de base de datos cuando cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4a234-507">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="4a234-507">Initial migration</span></span>

<span data-ttu-id="4a234-508">En esta sección, se completan las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a234-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="4a234-509">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="4a234-509">Add an initial migration.</span></span>
* <span data-ttu-id="4a234-510">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="4a234-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4a234-512">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="4a234-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="4a234-514">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4a234-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="4a234-515">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="4a234-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="4a234-516">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="4a234-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="4a234-517">El argumento `Initial` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="4a234-518">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="4a234-519">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="4a234-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="4a234-520">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/{time-stamp}_InitialCreate.cs* , con lo que se crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-521">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="4a234-522">El comando `ef migrations add InitialCreate` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="4a234-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="4a234-523">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="4a234-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="4a234-524">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="4a234-525">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="4a234-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4a234-526">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="4a234-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4a234-527">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a234-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4a234-528">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a234-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="4a234-529">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="4a234-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4a234-530">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a234-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a234-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a234-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a234-532">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="4a234-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="4a234-533">Consulte el siguiente método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4a234-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4a234-534">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="4a234-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="4a234-535">El elemento `MvcMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4a234-536">El contexto de datos (`MvcMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="4a234-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4a234-537">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="4a234-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="4a234-538">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="4a234-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4a234-539">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4a234-540">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="4a234-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4a234-541">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="4a234-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4a234-542">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="4a234-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a234-543">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a234-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4a234-544">Ha creado un contexto de base de datos y lo ha registrado con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="4a234-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4a234-545">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="4a234-545">Test the app</span></span>

* <span data-ttu-id="4a234-546">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="4a234-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4a234-547">Si se produce una excepción de base de datos similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a234-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4a234-548">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="4a234-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4a234-549">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a234-549">Test the **Create** link.</span></span> <span data-ttu-id="4a234-550">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="4a234-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="4a234-551">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="4a234-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4a234-552">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="4a234-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4a234-553">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="4a234-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4a234-554">Pruebe los vínculos **Editar** , **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="4a234-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="4a234-555">Examine la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="4a234-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="4a234-556">En el código resaltado anterior se muestra cómo se agrega el contexto de base de datos de películas al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="4a234-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="4a234-557">`services.AddDbContext<MvcMovieContext>(options =>` especifica la base de datos que se usará y la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="4a234-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="4a234-558">`=>` es un [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="4a234-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="4a234-559">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="4a234-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="4a234-560">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="4a234-561">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a234-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="4a234-562">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="4a234-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="4a234-563">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="4a234-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="4a234-564">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="4a234-565">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="4a234-566">Este enfoque fuertemente tipado permite una mejor comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="4a234-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="4a234-567">El mecanismo de scaffolding usó este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas cuando creó los métodos y las vistas.</span><span class="sxs-lookup"><span data-stu-id="4a234-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="4a234-568">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="4a234-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="4a234-569">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="4a234-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="4a234-570">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="4a234-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="4a234-571">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="4a234-572">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="4a234-573">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="4a234-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="4a234-574">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="4a234-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="4a234-575">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="4a234-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="4a234-576">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="4a234-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="4a234-577">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="4a234-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="4a234-578">Examine el contenido del archivo *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="4a234-579">Mediante la inclusión de una instrucción `@model` en la parte superior del archivo de vista, puede especificar el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="4a234-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="4a234-580">Al crear el controlador de película, se incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="4a234-581">Esta directiva `@model` permite acceder a la película que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="4a234-582">Por ejemplo, en la vista *Details.cshtml* , el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="4a234-583">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="4a234-584">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="4a234-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="4a234-585">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="4a234-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="4a234-586">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="4a234-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="4a234-587">Cuando se creó el controlador movies, el scaffolding incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4a234-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="4a234-588">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="4a234-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="4a234-589">Por ejemplo, en la vista *Index.cshtml* , el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="4a234-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="4a234-590">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4a234-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="4a234-591">Entre otras ventajas, esto implica que se obtiene una comprobación del código en tiempo de compilación:</span><span class="sxs-lookup"><span data-stu-id="4a234-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a234-592">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a234-592">Additional resources</span></span>

* [<span data-ttu-id="4a234-593">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="4a234-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="4a234-594">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="4a234-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="4a234-595">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con una base de datos](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="4a234-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
