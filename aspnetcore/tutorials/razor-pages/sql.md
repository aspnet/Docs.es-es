---
title: Parte 4, trabajo con una base de datos
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/26/2020
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 6627825239d815d2c8bcad98e22340f07f921ab9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420024"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="59854-103">Parte 4 de la serie de tutoriales sobre Razor Pages</span><span class="sxs-lookup"><span data-stu-id="59854-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="59854-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="59854-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="59854-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="59854-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="59854-106">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="59854-107">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="59854-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-109">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="59854-110">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="59854-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="59854-111">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="59854-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59854-113">La cadena de conexión generada será similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="59854-115">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="59854-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="59854-116">Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="59854-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="59854-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="59854-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="59854-119">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="59854-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="59854-120">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="59854-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="59854-121">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="59854-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="59854-122">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="59854-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menú Ver](sql/_static/5/ssox.png)

1. <span data-ttu-id="59854-124">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="59854-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Menús contextuales abiertos en la tabla Movie](sql/_static/5/design.png)

   ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

   <span data-ttu-id="59854-127">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="59854-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="59854-128">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="59854-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="59854-129">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="59854-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="59854-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="59854-132">SQLite</span></span>

<span data-ttu-id="59854-133">Según la información del sitio web de [SQLite](https://www.sqlite.org/):</span><span class="sxs-lookup"><span data-stu-id="59854-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="59854-134">SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público.</span><span class="sxs-lookup"><span data-stu-id="59854-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="59854-135">SQLite es el motor de base de datos más usado en el mundo.</span><span class="sxs-lookup"><span data-stu-id="59854-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="59854-136">Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="59854-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="59854-137">La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="59854-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="59854-138">Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.</span><span class="sxs-lookup"><span data-stu-id="59854-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="59854-140">Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="59854-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="59854-141">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="59854-142">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="59854-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="59854-143">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="59854-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="59854-144">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="59854-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="59854-145">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="59854-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="59854-146">En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.</span><span class="sxs-lookup"><span data-stu-id="59854-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="59854-147">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="59854-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="59854-148">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="59854-149">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="59854-149">Creating a new table.</span></span>
>* <span data-ttu-id="59854-150">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="59854-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="59854-151">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="59854-151">Dropping the old table.</span></span>
>* <span data-ttu-id="59854-152">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="59854-152">Renaming the new table.</span></span>
>
><span data-ttu-id="59854-153">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="59854-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="59854-154">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="59854-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="59854-155">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="59854-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="59854-156">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="59854-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="59854-157">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="59854-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="59854-158">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="59854-158">Seed the database</span></span>

<span data-ttu-id="59854-159">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="59854-160">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="59854-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="59854-161">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="59854-161">Add the seed initializer</span></span>

<span data-ttu-id="59854-162">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="59854-163">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="59854-164">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="59854-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="59854-165">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="59854-166">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="59854-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="59854-167">La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="59854-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="59854-168">La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:</span><span class="sxs-lookup"><span data-stu-id="59854-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="59854-169">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="59854-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="59854-171">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-171">Delete all the records in the database.</span></span> <span data-ttu-id="59854-172">Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="59854-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="59854-173">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="59854-174">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="59854-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="59854-175">Detenga y reinicie IIS con cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="59854-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="59854-176">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y seleccione **Salir** o **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="59854-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menú contextual](sql/_static/stopIIS.png)

   1. <span data-ttu-id="59854-179">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="59854-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="59854-180">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="59854-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-181">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="59854-182">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="59854-183">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="59854-184">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="59854-184">The app shows the seeded data:</span></span>

![Aplicación de películas abierta en el explorador donde se muestran los datos de películas](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="59854-186">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="59854-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="59854-187">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="59854-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="59854-188">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="59854-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="59854-189">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="59854-190">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="59854-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-192">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="59854-193">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="59854-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="59854-194">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="59854-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59854-196">La cadena de conexión generada será similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-197">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="59854-198">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="59854-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="59854-199">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="59854-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="59854-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="59854-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="59854-202">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="59854-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="59854-203">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="59854-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="59854-204">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="59854-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="59854-205">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="59854-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="59854-207">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="59854-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menús contextuales abiertos en la tabla Movie](sql/_static/design.png)

  ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

<span data-ttu-id="59854-210">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="59854-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="59854-211">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="59854-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="59854-212">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="59854-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-214">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="59854-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="59854-215">SQLite</span></span>

<span data-ttu-id="59854-216">Según la información del sitio web de [SQLite](https://www.sqlite.org/):</span><span class="sxs-lookup"><span data-stu-id="59854-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="59854-217">SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público.</span><span class="sxs-lookup"><span data-stu-id="59854-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="59854-218">SQLite es el motor de base de datos más usado en el mundo.</span><span class="sxs-lookup"><span data-stu-id="59854-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="59854-219">Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="59854-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="59854-220">La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="59854-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="59854-221">Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.</span><span class="sxs-lookup"><span data-stu-id="59854-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="59854-223">Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="59854-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="59854-224">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="59854-225">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="59854-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="59854-226">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="59854-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="59854-227">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="59854-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="59854-228">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="59854-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="59854-229">En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.</span><span class="sxs-lookup"><span data-stu-id="59854-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="59854-230">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="59854-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="59854-231">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="59854-232">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="59854-232">Creating a new table.</span></span>
>* <span data-ttu-id="59854-233">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="59854-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="59854-234">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="59854-234">Dropping the old table.</span></span>
>* <span data-ttu-id="59854-235">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="59854-235">Renaming the new table.</span></span>
>
><span data-ttu-id="59854-236">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="59854-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="59854-237">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="59854-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="59854-238">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="59854-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="59854-239">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="59854-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="59854-240">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="59854-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="59854-241">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="59854-241">Seed the database</span></span>

<span data-ttu-id="59854-242">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="59854-243">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="59854-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="59854-244">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="59854-244">Add the seed initializer</span></span>

<span data-ttu-id="59854-245">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="59854-246">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="59854-247">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="59854-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="59854-248">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="59854-249">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="59854-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="59854-250">La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="59854-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="59854-251">La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:</span><span class="sxs-lookup"><span data-stu-id="59854-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="59854-252">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="59854-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59854-254">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-254">Delete all the records in the database.</span></span> <span data-ttu-id="59854-255">Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="59854-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="59854-256">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="59854-257">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="59854-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="59854-258">Detenga y reinicie IIS con cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="59854-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="59854-259">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="59854-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="59854-262">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="59854-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="59854-263">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="59854-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-264">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="59854-265">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="59854-266">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="59854-267">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="59854-267">The app shows the seeded data:</span></span>

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="59854-269">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="59854-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="59854-270">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="59854-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59854-271">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="59854-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="59854-272">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="59854-273">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="59854-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="59854-275">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="59854-276">Para más información sobre los modelos empleados en `ConfigureServices`, vea:</span><span class="sxs-lookup"><span data-stu-id="59854-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="59854-277">[Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="59854-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="59854-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="59854-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="59854-279">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="59854-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="59854-280">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="59854-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59854-282">La cadena de conexión generada será similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="59854-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59854-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59854-284">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="59854-285">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="59854-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="59854-286">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="59854-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="59854-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="59854-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="59854-289">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="59854-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="59854-290">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="59854-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="59854-291">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="59854-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="59854-292">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="59854-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="59854-294">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="59854-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menú contextual abierto en la tabla Movie](sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](sql/_static/dv.png)

<span data-ttu-id="59854-297">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="59854-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="59854-298">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="59854-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="59854-299">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="59854-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="59854-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59854-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59854-302">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="59854-303">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="59854-303">Seed the database</span></span>

<span data-ttu-id="59854-304">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="59854-305">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="59854-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="59854-306">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="59854-306">Add the seed initializer</span></span>

<span data-ttu-id="59854-307">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="59854-308">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="59854-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="59854-309">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="59854-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="59854-310">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="59854-311">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="59854-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="59854-312">La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="59854-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="59854-313">Una aplicación de producción no llamaría a `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="59854-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="59854-314">Se agrega al código anterior para evitar que se produzca la siguiente excepción cuando `Update-Database` no se ha ejecutado:</span><span class="sxs-lookup"><span data-stu-id="59854-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="59854-315">SqlException: No se puede abrir la base de datos "RazorPagesMovieContext-21" solicitada por el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="59854-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="59854-316">Error de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="59854-316">The login failed.</span></span>
<span data-ttu-id="59854-317">Error de inicio de sesión del usuario <nombre de usuario>.</span><span class="sxs-lookup"><span data-stu-id="59854-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="59854-318">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="59854-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59854-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59854-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59854-320">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-320">Delete all the records in the database.</span></span> <span data-ttu-id="59854-321">Puede hacerlo con los vínculos de eliminación en el explorador o desde [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="59854-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="59854-322">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="59854-323">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="59854-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="59854-324">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="59854-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="59854-325">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="59854-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="59854-328">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="59854-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="59854-329">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="59854-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59854-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59854-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59854-331">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="59854-332">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="59854-333">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="59854-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="59854-334">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="59854-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="59854-335">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="59854-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="59854-336">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="59854-336">The app shows the seeded data:</span></span>

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

<span data-ttu-id="59854-338">El siguiente tutorial limpia la presentación de los datos.</span><span class="sxs-lookup"><span data-stu-id="59854-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59854-339">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="59854-339">Additional resources</span></span>

* [<span data-ttu-id="59854-340">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="59854-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="59854-341">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="59854-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
