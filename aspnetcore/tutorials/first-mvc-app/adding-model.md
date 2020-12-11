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
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855526"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="64ba5-103">Parte 4. Adición de un modelo a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="64ba5-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="64ba5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="64ba5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="64ba5-105">En esta sección, agregará las clases para administrar películas en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="64ba5-106">Estas clases serán el elemento "**M** odel" de la aplicación **M** VC.</span><span class="sxs-lookup"><span data-stu-id="64ba5-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="64ba5-107">Estas clases se usan con [Entity Framework Core](/ef/core) (EF Core) para trabajar con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="64ba5-108">EF Core es un marco de trabajo de asignación relacional de objetos (ORM) que simplifica el código de acceso de datos que se debe escribir.</span><span class="sxs-lookup"><span data-stu-id="64ba5-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="64ba5-109">Las clases de modelo que se crean se conocen como clases POCO (del inglés "**p** lain **O** ld **C** LR **O** bjects", objetos CLR antiguos sin formato) porque no tienen ninguna dependencia de EF Core.</span><span class="sxs-lookup"><span data-stu-id="64ba5-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="64ba5-110">Simplemente definen las propiedades de los datos que se almacenan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="64ba5-111">En este tutorial, se escriben primero las clases del modelo y EF Core crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="64ba5-112">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-114">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="64ba5-115">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ba5-117">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="64ba5-119">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="64ba5-120">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="64ba5-121">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="64ba5-122">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="64ba5-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="64ba5-123">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="64ba5-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="64ba5-124">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="64ba5-124">With this attribute:</span></span>

* <span data-ttu-id="64ba5-125">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="64ba5-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="64ba5-126">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="64ba5-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="64ba5-127">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="64ba5-128">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="64ba5-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-130">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="64ba5-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="64ba5-132">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="64ba5-133">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="64ba5-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="64ba5-134">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="64ba5-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="64ba5-135">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="64ba5-138">En el menú **Proyecto**, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="64ba5-139">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="64ba5-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="64ba5-140">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="64ba5-142">Se mostrará el cuadro de diálogo **Seleccionar los proyectos**, con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="64ba5-143">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-143">Press the **Ok** button.</span></span>

<span data-ttu-id="64ba5-144">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="64ba5-145">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="64ba5-146">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="64ba5-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="64ba5-147">Ejecute el comando siguiente de la CLI de .NET:</span><span class="sxs-lookup"><span data-stu-id="64ba5-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="64ba5-148">El comando anterior agrega la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="64ba5-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="64ba5-149">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-149">Create a database context class</span></span>

<span data-ttu-id="64ba5-150">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="64ba5-151">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="64ba5-152">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-152">Create a *Data* folder.</span></span>

<span data-ttu-id="64ba5-153">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="64ba5-154">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="64ba5-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="64ba5-155">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="64ba5-156">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="64ba5-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="64ba5-157">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-157">Register the database context</span></span>

<span data-ttu-id="64ba5-158">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="64ba5-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64ba5-159">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="64ba5-160">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="64ba5-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64ba5-161">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="64ba5-162">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="64ba5-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="64ba5-163">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="64ba5-164">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="64ba5-167">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="64ba5-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="64ba5-168">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="64ba5-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="64ba5-169">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-169">Add a database connection string</span></span>

<span data-ttu-id="64ba5-170">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="64ba5-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-172">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="64ba5-173">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="64ba5-174">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="64ba5-174">Scaffold movie pages</span></span>

<span data-ttu-id="64ba5-175">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="64ba5-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-177">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="64ba5-179">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="64ba5-181">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="64ba5-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="64ba5-182">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="64ba5-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="64ba5-183">**Clase de contexto de datos**: *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="64ba5-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc5.png)

* <span data-ttu-id="64ba5-185">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="64ba5-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="64ba5-186">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="64ba5-187">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-187">Select **Add**</span></span>

<span data-ttu-id="64ba5-188">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="64ba5-188">Visual Studio creates:</span></span>

* <span data-ttu-id="64ba5-189">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="64ba5-190">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="64ba5-191">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="64ba5-193">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="64ba5-194">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="64ba5-195">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="64ba5-197">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="64ba5-198">Exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="64ba5-199">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="64ba5-200">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="64ba5-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="64ba5-201">Si ejecuta la aplicación y hace clic en el vínculo **Movie App**, obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="64ba5-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="64ba5-202">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="64ba5-202">Initial migration</span></span>

<span data-ttu-id="64ba5-203">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="64ba5-204">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-206">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="64ba5-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="64ba5-207">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="64ba5-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="64ba5-208">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="64ba5-209">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="64ba5-210">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="64ba5-211">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="64ba5-212">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="64ba5-213">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="64ba5-214">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="64ba5-215">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="64ba5-216">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="64ba5-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="64ba5-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="64ba5-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="64ba5-218">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="64ba5-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="64ba5-219">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-220">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="64ba5-221">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="64ba5-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="64ba5-222">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="64ba5-223">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="64ba5-224">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="64ba5-225">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="64ba5-226">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="64ba5-227">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="64ba5-228">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="64ba5-229">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="64ba5-229">The InitialCreate class</span></span>

<span data-ttu-id="64ba5-230">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="64ba5-231">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="64ba5-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="64ba5-232">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="64ba5-233">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="64ba5-233">Test the app</span></span>

* <span data-ttu-id="64ba5-234">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="64ba5-235">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="64ba5-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-237">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="64ba5-238">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="64ba5-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="64ba5-239">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-239">Test the **Create** page.</span></span> <span data-ttu-id="64ba5-240">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="64ba5-241">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="64ba5-242">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="64ba5-243">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="64ba5-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="64ba5-244">Pruebe las páginas **Edit**, **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="64ba5-245">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="64ba5-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-247">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="64ba5-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="64ba5-248">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="64ba5-249">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-250">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="64ba5-251">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="64ba5-252">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="64ba5-253">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="64ba5-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="64ba5-254">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="64ba5-255">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="64ba5-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="64ba5-256">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="64ba5-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="64ba5-257">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="64ba5-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="64ba5-258">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="64ba5-259">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="64ba5-260">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="64ba5-261">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="64ba5-262">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="64ba5-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="64ba5-263">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="64ba5-264">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="64ba5-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="64ba5-265">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="64ba5-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="64ba5-266">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="64ba5-267">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="64ba5-268">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="64ba5-269">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="64ba5-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="64ba5-270">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="64ba5-271">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="64ba5-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="64ba5-272">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="64ba5-273">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="64ba5-274">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="64ba5-275">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="64ba5-276">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="64ba5-277">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="64ba5-278">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="64ba5-279">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="64ba5-280">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="64ba5-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="64ba5-281">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="64ba5-282">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="64ba5-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="64ba5-283">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="64ba5-284">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="64ba5-285">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="64ba5-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="64ba5-286">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="64ba5-287">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64ba5-288">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="64ba5-288">Additional resources</span></span>

* [<span data-ttu-id="64ba5-289">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="64ba5-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="64ba5-290">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="64ba5-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="64ba5-291">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="64ba5-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="64ba5-292">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-294">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="64ba5-295">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ba5-297">Agregue un archivo denominado *Movie.cs* a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-298">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="64ba5-299">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Nueva clase** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="64ba5-300">Asigne el nombre *Movie.cs* al archivo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="64ba5-301">Actualice el archivo *Movie.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="64ba5-302">La clase `Movie` contiene un campo `Id`, que la base de datos requiere para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="64ba5-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="64ba5-303">El atributo <xref:System.ComponentModel.DataAnnotations.DataType> en `ReleaseDate` especifica el tipo de los datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="64ba5-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="64ba5-304">Con este atributo:</span><span class="sxs-lookup"><span data-stu-id="64ba5-304">With this attribute:</span></span>

* <span data-ttu-id="64ba5-305">El usuario no tiene que especificar información horaria en el campo de fecha.</span><span class="sxs-lookup"><span data-stu-id="64ba5-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="64ba5-306">Solo se muestra la fecha, no información horaria.</span><span class="sxs-lookup"><span data-stu-id="64ba5-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="64ba5-307">Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="64ba5-308">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="64ba5-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-310">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="64ba5-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="64ba5-312">En la Consola del administrador de paquetes, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="64ba5-313">El comando anterior agrega el proveedor de SQL Server de EF Core.</span><span class="sxs-lookup"><span data-stu-id="64ba5-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="64ba5-314">El paquete de proveedor instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="64ba5-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="64ba5-315">Los paquetes adicionales se instalan de forma automática en el paso de scaffolding más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-317">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="64ba5-318">En el menú **Proyecto**, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="64ba5-319">En el campo de **búsqueda** de la esquina superior derecha, escriba `Microsoft.EntityFrameworkCore.SQLite` y presione la tecla **Entrar** para realizar la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="64ba5-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="64ba5-320">Seleccione el paquete NuGet correspondiente y presione el botón **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adición del paquete NuGet de Entity Framework Core](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="64ba5-322">Se mostrará el cuadro de diálogo **Seleccionar los proyectos**, con el proyecto `MvcMovie` seleccionado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="64ba5-323">Presione el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-323">Press the **Ok** button.</span></span>

<span data-ttu-id="64ba5-324">Se mostrará un cuadro de diálogo **Aceptación de licencia**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="64ba5-325">Revise las licencias como quiera y, a continuación, haga clic en el botón **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="64ba5-326">Repita los pasos anteriores para instalar los paquetes NuGet siguientes:</span><span class="sxs-lookup"><span data-stu-id="64ba5-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="64ba5-327">Creación de una clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-327">Create a database context class</span></span>

<span data-ttu-id="64ba5-328">Se necesita una clase de contexto de base de datos para coordinar la funcionalidad de EF Core (crear, leer, actualizar, eliminar) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="64ba5-329">El contexto de base de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) y especifica las entidades que se van a incluir en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="64ba5-330">Cree una carpeta *Data*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-330">Create a *Data* folder.</span></span>

<span data-ttu-id="64ba5-331">Agregue un archivo *Data/MvcMovieContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="64ba5-332">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="64ba5-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="64ba5-333">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="64ba5-334">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="64ba5-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="64ba5-335">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-335">Register the database context</span></span>

<span data-ttu-id="64ba5-336">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="64ba5-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64ba5-337">Los servicios (como el contexto de base de datos de EF Core) se deben registrar con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="64ba5-338">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="64ba5-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64ba5-339">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="64ba5-340">En esta sección, se registra el contexto de base de datos con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="64ba5-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="64ba5-341">Agregue las instrucciones `using` siguientes en la parte superior de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="64ba5-342">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-344">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="64ba5-345">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="64ba5-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="64ba5-346">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="64ba5-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="64ba5-347">Agregar una cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-347">Add a database connection string</span></span>

<span data-ttu-id="64ba5-348">Agregue una cadena de conexión al *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="64ba5-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-350">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="64ba5-351">Compile el proyecto para comprobar si hay errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="64ba5-352">Scaffolding de las páginas de películas</span><span class="sxs-lookup"><span data-stu-id="64ba5-352">Scaffold movie pages</span></span>

<span data-ttu-id="64ba5-353">Use la herramienta de scaffolding para crear páginas de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="64ba5-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-355">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="64ba5-357">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="64ba5-359">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="64ba5-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="64ba5-360">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="64ba5-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="64ba5-361">**Clase de contexto de datos**: *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="64ba5-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adición de contexto de datos](adding-model/_static/dc3.png)

* <span data-ttu-id="64ba5-363">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="64ba5-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="64ba5-364">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="64ba5-365">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-365">Select **Add**</span></span>

<span data-ttu-id="64ba5-366">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="64ba5-366">Visual Studio creates:</span></span>

* <span data-ttu-id="64ba5-367">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="64ba5-368">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="64ba5-369">La creación automática de estos archivos se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="64ba5-371">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="64ba5-372">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="64ba5-373">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-374">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="64ba5-375">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="64ba5-376">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="64ba5-377">Todavía no se pueden usar las páginas con scaffolding porque la base de datos no existe.</span><span class="sxs-lookup"><span data-stu-id="64ba5-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="64ba5-378">Si ejecuta la aplicación y hace clic en el vínculo **Movie App**, obtendrá un mensaje de error *Cannot open database* (No se puede abrir la base de datos) o *no such table: Movie* (no existe la tabla: Movie).</span><span class="sxs-lookup"><span data-stu-id="64ba5-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="64ba5-379">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="64ba5-379">Initial migration</span></span>

<span data-ttu-id="64ba5-380">Use la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="64ba5-381">Las migraciones son un conjunto de herramientas que permiten crear y actualizar una base de datos para que coincida con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-383">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="64ba5-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="64ba5-384">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="64ba5-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="64ba5-385">`Add-Migration InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="64ba5-386">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="64ba5-387">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="64ba5-388">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="64ba5-389">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="64ba5-390">`Update-Database`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="64ba5-391">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="64ba5-392">El comando de actualización de la base de datos genera la advertencia siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="64ba5-393">No type was specified for the decimal column "Price" on entity type "Movie" (No se ha especificado ningún tipo en la columna decimal "Price" en el tipo de entidad "Movie").</span><span class="sxs-lookup"><span data-stu-id="64ba5-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="64ba5-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="64ba5-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="64ba5-395">Explicitly specify the SQL server column type that can accommodate all the values using "HasColumnType()" (Especifique de forma explícita el tipo de columna de SQL Server que pueda acomodar todos los valores mediante "HasColumnType()").</span><span class="sxs-lookup"><span data-stu-id="64ba5-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="64ba5-396">Puede omitir dicha advertencia, ya que se corregirá en un tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-397">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="64ba5-398">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="64ba5-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="64ba5-399">`ef migrations add InitialCreate`: Genera un archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="64ba5-400">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="64ba5-401">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="64ba5-402">Como se trata de la primera migración, la clase generada contiene código para crear el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="64ba5-403">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="64ba5-404">`ef database update`: actualiza la base de datos a la migración más reciente, que ha creado el comando anterior.</span><span class="sxs-lookup"><span data-stu-id="64ba5-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="64ba5-405">El comando ejecuta el método `Up` en el archivo *Migrations/{marca de tiempo}_InitialCreate.cs*, que crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="64ba5-406">La clase InitialCreate</span><span class="sxs-lookup"><span data-stu-id="64ba5-406">The InitialCreate class</span></span>

<span data-ttu-id="64ba5-407">Examine el archivo de migración *Migrations/{marca de tiempo}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="64ba5-408">El método `Up` crea la tabla Movie y configura `Id` como la clave principal.</span><span class="sxs-lookup"><span data-stu-id="64ba5-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="64ba5-409">El método `Down` revierte los cambios de esquema realizados por la migración `Up`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="64ba5-410">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="64ba5-410">Test the app</span></span>

* <span data-ttu-id="64ba5-411">Ejecute la aplicación y haga clic en el vínculo **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="64ba5-412">Si obtiene una excepción similar a una de las siguientes:</span><span class="sxs-lookup"><span data-stu-id="64ba5-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-414">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="64ba5-415">Probablemente haya omitido el [paso de migraciones](#migration).</span><span class="sxs-lookup"><span data-stu-id="64ba5-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="64ba5-416">Pruebe la página **Create**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-416">Test the **Create** page.</span></span> <span data-ttu-id="64ba5-417">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="64ba5-418">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="64ba5-419">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="64ba5-420">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="64ba5-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="64ba5-421">Pruebe las páginas **Edit**, **Details** y **Delete**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="64ba5-422">Inserción de dependencias en el controlador</span><span class="sxs-lookup"><span data-stu-id="64ba5-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-424">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="64ba5-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="64ba5-425">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="64ba5-426">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-427">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="64ba5-428">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="64ba5-429">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="64ba5-430">Uso de SQLite para desarrollo y SQL Server para producción</span><span class="sxs-lookup"><span data-stu-id="64ba5-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="64ba5-431">Cuando se selecciona SQLite, el código generado por la plantilla está listo para desarrollo.</span><span class="sxs-lookup"><span data-stu-id="64ba5-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="64ba5-432">En el código siguiente se muestra cómo insertar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> en el inicio.</span><span class="sxs-lookup"><span data-stu-id="64ba5-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="64ba5-433">`IWebHostEnvironment` se inserta para que `ConfigureServices` pueda usar SQLite en desarrollo y SQL Server en producción.</span><span class="sxs-lookup"><span data-stu-id="64ba5-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="64ba5-434">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="64ba5-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="64ba5-435">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="64ba5-436">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="64ba5-437">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="64ba5-438">Este enfoque fuertemente tipado permite comprobar el código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="64ba5-439">En el mecanismo de scaffolding se ha usado este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas.</span><span class="sxs-lookup"><span data-stu-id="64ba5-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="64ba5-440">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="64ba5-441">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="64ba5-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="64ba5-442">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="64ba5-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="64ba5-443">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="64ba5-444">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="64ba5-445">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="64ba5-446">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="64ba5-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="64ba5-447">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="64ba5-448">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="64ba5-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="64ba5-449">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="64ba5-450">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="64ba5-451">La instrucción `@model` de la parte superior del archivo de vista especifica el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="64ba5-452">Cuando se ha creado el controlador de película, se ha incluido la siguiente instrucción `@model`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="64ba5-453">Esta directiva `@model` permite el acceso a la película que el controlador ha pasado a la vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="64ba5-454">El objeto `Model` está fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="64ba5-455">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="64ba5-456">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="64ba5-457">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="64ba5-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="64ba5-458">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="64ba5-459">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="64ba5-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="64ba5-460">Cuando se ha creado el controlador de películas, el scaffolding ha incluido la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="64ba5-461">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="64ba5-462">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="64ba5-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="64ba5-463">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="64ba5-464">Entre otras ventajas, esto implica que se obtiene la comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64ba5-465">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="64ba5-465">Additional resources</span></span>

* [<span data-ttu-id="64ba5-466">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="64ba5-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="64ba5-467">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="64ba5-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="64ba5-468">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="64ba5-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="64ba5-469">Agregar una clase de modelo de datos</span><span class="sxs-lookup"><span data-stu-id="64ba5-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-471">Haga clic con el botón derecho en la carpeta *Models* > **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="64ba5-472">Asigne a la clase el nombre **Película**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-473">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="64ba5-474">Agregue una clase a la carpeta *Modelos* denominada *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="64ba5-475">Aplicar scaffolding al modelo de película</span><span class="sxs-lookup"><span data-stu-id="64ba5-475">Scaffold the movie model</span></span>

<span data-ttu-id="64ba5-476">En esta sección se aplica scaffolding al modelo de película;</span><span class="sxs-lookup"><span data-stu-id="64ba5-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="64ba5-477">es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de película.</span><span class="sxs-lookup"><span data-stu-id="64ba5-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-479">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Controladores* **> Agregar > Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del paso anterior](adding-model/_static/add_controller21.png)

<span data-ttu-id="64ba5-481">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC con vistas que usan Entity Framework > Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Cuadro de diálogo Agregar scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="64ba5-483">Rellene el cuadro de diálogo **Agregar controlador**:</span><span class="sxs-lookup"><span data-stu-id="64ba5-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="64ba5-484">**Clase de modelo**: *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="64ba5-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="64ba5-485">**Clase de contexto de datos**: seleccione el icono **+** y agregue el valor predeterminado **MvcMovie.Models.MvcMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adición de contexto de datos](adding-model/_static/dc.png)

* <span data-ttu-id="64ba5-487">**Vistas**: conserve el valor predeterminado de cada opción activada.</span><span class="sxs-lookup"><span data-stu-id="64ba5-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="64ba5-488">**Nombre del controlador**: conserve el valor predeterminado *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="64ba5-489">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-489">Select **Add**</span></span>

![Cuadro de diálogo Agregar controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="64ba5-491">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="64ba5-491">Visual Studio creates:</span></span>

* <span data-ttu-id="64ba5-492">Una [clase de contexto de base de datos](xref:data/ef-mvc/intro#create-the-database-context) de Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="64ba5-493">Un controlador de películas (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="64ba5-494">Archivos de vistas de Razor para las páginas de creación, eliminación, detalles, edición e índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="64ba5-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="64ba5-495">La creación automática del contexto de base de datos y de vistas y métodos de acción [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (crear, leer, actualizar y eliminar) se conoce como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="64ba5-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ba5-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ba5-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="64ba5-497">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="64ba5-498">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="64ba5-499">En Linux, exporte la ruta de acceso de la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="64ba5-500">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-501">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="64ba5-502">Abra una ventana de comandos en el directorio del proyecto (el directorio que contiene los archivos *Program.cs*, *Startup.cs* y *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="64ba5-503">Instale la herramienta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="64ba5-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="64ba5-504">Ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="64ba5-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="64ba5-505">Si ejecuta la aplicación y hace clic en el vínculo **Mvc Movie**, aparece un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-507">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="64ba5-508">Debe crear la base de datos y usar para ello la característica [Migraciones](xref:data/ef-mvc/migrations) de EF Core.</span><span class="sxs-lookup"><span data-stu-id="64ba5-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="64ba5-509">Las migraciones permiten crear una base de datos que coincide con el modelo de datos y actualizan el esquema de base de datos cuando cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="64ba5-510">Migración inicial</span><span class="sxs-lookup"><span data-stu-id="64ba5-510">Initial migration</span></span>

<span data-ttu-id="64ba5-511">En esta sección, se completan las tareas siguientes:</span><span class="sxs-lookup"><span data-stu-id="64ba5-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="64ba5-512">Agregar una migración inicial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-512">Add an initial migration.</span></span>
* <span data-ttu-id="64ba5-513">Actualizar la base de datos con la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="64ba5-515">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="64ba5-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menú de PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="64ba5-517">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="64ba5-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="64ba5-518">El comando `Add-Migration` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="64ba5-519">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="64ba5-520">El argumento `Initial` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="64ba5-521">Se puede usar cualquier nombre, pero, por convención, se utiliza uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="64ba5-522">Para obtener más información, vea <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="64ba5-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="64ba5-523">El comando `Update-Database` ejecuta el método `Up` en el archivo *Migrations/{time-stamp}_InitialCreate.cs*, con lo que se crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-524">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="64ba5-525">El comando `ef migrations add InitialCreate` genera el código para crear el esquema de base de datos inicial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="64ba5-526">El esquema de la base de datos se basa en el modelo especificado en la clase `MvcMovieContext` (en el archivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="64ba5-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="64ba5-527">El argumento `InitialCreate` es el nombre de la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="64ba5-528">Se puede usar cualquier nombre, pero, por convención, se selecciona uno que describa la migración.</span><span class="sxs-lookup"><span data-stu-id="64ba5-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="64ba5-529">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="64ba5-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="64ba5-530">ASP.NET Core integra la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="64ba5-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64ba5-531">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="64ba5-532">Estos servicios se proporcionan a los componentes que los necesitan (como Razor Pages) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="64ba5-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64ba5-533">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ba5-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ba5-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ba5-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ba5-535">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="64ba5-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="64ba5-536">Consulte el siguiente método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="64ba5-537">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="64ba5-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="64ba5-538">El elemento `MvcMovieContext` coordina la funcionalidad de EF Core (creación, lectura, actualización, eliminación, etc.) para el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="64ba5-539">El contexto de datos (`MvcMovieContext`) se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="64ba5-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="64ba5-540">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="64ba5-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="64ba5-541">En el código anterior se crea una propiedad [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para el conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="64ba5-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="64ba5-542">En la terminología de Entity Framework, un conjunto de entidades suele corresponder a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="64ba5-543">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="64ba5-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="64ba5-544">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="64ba5-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="64ba5-545">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="64ba5-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64ba5-546">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="64ba5-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="64ba5-547">Ha creado un contexto de base de datos y lo ha registrado con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="64ba5-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="64ba5-548">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="64ba5-548">Test the app</span></span>

* <span data-ttu-id="64ba5-549">Ejecute la aplicación y anexe `/Movies` a la dirección URL en el explorador ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="64ba5-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="64ba5-550">Si se produce una excepción de base de datos similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="64ba5-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="64ba5-551">Quiere decir que falta el [paso de migraciones](#pmc).</span><span class="sxs-lookup"><span data-stu-id="64ba5-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="64ba5-552">Pruebe el vínculo **Crear**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-552">Test the **Create** link.</span></span> <span data-ttu-id="64ba5-553">Escriba y envíe los datos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="64ba5-554">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="64ba5-555">La aplicación debe globalizarse para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos.</span><span class="sxs-lookup"><span data-stu-id="64ba5-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="64ba5-556">Para obtener instrucciones sobre la globalización, consulte [esta cuestión en GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="64ba5-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="64ba5-557">Pruebe los vínculos **Editar**, **Detalles** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="64ba5-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="64ba5-558">Examine la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="64ba5-559">En el código resaltado anterior se muestra cómo se agrega el contexto de base de datos de películas al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="64ba5-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="64ba5-560">`services.AddDbContext<MvcMovieContext>(options =>` especifica la base de datos que se usará y la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="64ba5-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="64ba5-561">`=>` es un [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="64ba5-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="64ba5-562">Abra el archivo *Controllers/MoviesController.cs* y examine el constructor:</span><span class="sxs-lookup"><span data-stu-id="64ba5-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="64ba5-563">El constructor usa la [inserción de dependencias](xref:fundamentals/dependency-injection) para insertar el contexto de base de datos (`MvcMovieContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="64ba5-564">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="64ba5-565">Modelos fuertemente tipados y la palabra clave @model</span><span class="sxs-lookup"><span data-stu-id="64ba5-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="64ba5-566">Anteriormente en este tutorial, vimos cómo un controlador puede pasar datos u objetos a una vista mediante el diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="64ba5-567">El diccionario `ViewData` es un objeto dinámico que proporciona una cómoda manera enlazada en tiempo de ejecución de pasar información a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="64ba5-568">MVC también ofrece la capacidad de pasar objetos de modelo fuertemente tipados a una vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="64ba5-569">Este enfoque fuertemente tipado permite una mejor comprobación del código en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="64ba5-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="64ba5-570">El mecanismo de scaffolding usó este enfoque (que consiste en pasar un modelo fuertemente tipado) con la clase `MoviesController` y las vistas cuando creó los métodos y las vistas.</span><span class="sxs-lookup"><span data-stu-id="64ba5-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="64ba5-571">Examine el método `Details` generado en el archivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="64ba5-572">El parámetro `id` suele pasarse como datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="64ba5-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="64ba5-573">Por ejemplo, `https://localhost:5001/movies/details/1` establece:</span><span class="sxs-lookup"><span data-stu-id="64ba5-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="64ba5-574">El controlador en el controlador `movies` (el primer segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="64ba5-575">La acción en `details` (el segundo segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="64ba5-576">El identificador en 1 (el último segmento de dirección URL).</span><span class="sxs-lookup"><span data-stu-id="64ba5-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="64ba5-577">También puede pasar `id` con una cadena de consulta como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="64ba5-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="64ba5-578">El parámetro `id` se define como un [tipo que acepta valores NULL](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) en caso de que no se proporcione un valor de identificador.</span><span class="sxs-lookup"><span data-stu-id="64ba5-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="64ba5-579">Se pasa una [expresión lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) a `FirstOrDefaultAsync` para seleccionar entidades de película que coincidan con los datos de enrutamiento o el valor de consulta de cadena.</span><span class="sxs-lookup"><span data-stu-id="64ba5-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="64ba5-580">Si se encuentra una película, se pasa una instancia del modelo `Movie` a la vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="64ba5-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="64ba5-581">Examine el contenido del archivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="64ba5-582">Mediante la inclusión de una instrucción `@model` en la parte superior del archivo de vista, puede especificar el tipo de objeto que espera la vista.</span><span class="sxs-lookup"><span data-stu-id="64ba5-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="64ba5-583">Al crear el controlador de película, se incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="64ba5-584">Esta directiva `@model` permite acceder a la película que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="64ba5-585">Por ejemplo, en la vista *Details.cshtml*, el código pasa cada campo de película a los asistentes de HTML `DisplayNameFor` y `DisplayFor` con el objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="64ba5-586">Los métodos `Create` y `Edit` y las vistas también pasan un objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="64ba5-587">Examine la vista *Index.cshtml* y el método `Index` en el controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="64ba5-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="64ba5-588">Observe cómo el código crea un objeto `List` cuando llama al método `View`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="64ba5-589">El código pasa esta lista `Movies` desde el método de acción `Index` a la vista:</span><span class="sxs-lookup"><span data-stu-id="64ba5-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="64ba5-590">Cuando se creó el controlador movies, el scaffolding incluyó automáticamente la siguiente instrucción `@model` en la parte superior del archivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64ba5-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="64ba5-591">Esta directiva `@model` permite acceder a la lista de películas que el controlador pasó a la vista usando un objeto `Model` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64ba5-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="64ba5-592">Por ejemplo, en la vista *Index.cshtml*, el código recorre en bucle las películas con una instrucción `foreach` sobre el objeto `Model` fuertemente tipado:</span><span class="sxs-lookup"><span data-stu-id="64ba5-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="64ba5-593">Como el objeto `Model` es fuertemente tipado (como un objeto `IEnumerable<Movie>`), cada elemento del bucle está tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="64ba5-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="64ba5-594">Entre otras ventajas, esto implica que se obtiene una comprobación del código en tiempo de compilación:</span><span class="sxs-lookup"><span data-stu-id="64ba5-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64ba5-595">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="64ba5-595">Additional resources</span></span>

* [<span data-ttu-id="64ba5-596">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="64ba5-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="64ba5-597">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="64ba5-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="64ba5-598">[Anterior: Agregar una vista](adding-view.md)
> [Siguiente: Trabajar con una base de datos](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="64ba5-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
