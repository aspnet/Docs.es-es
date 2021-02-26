---
title: Parte 4, trabajo con una base de datos
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: fa1060ae1a046a40d55e9fef4a094aa9e51a18af
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101176"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a><span data-ttu-id="93780-103">Parte 4 de la serie de tutoriales sobre Razor Pages</span><span class="sxs-lookup"><span data-stu-id="93780-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="93780-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="93780-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="93780-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="93780-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="93780-106">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="93780-107">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="93780-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-109">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="93780-110">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="93780-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="93780-111">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93780-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93780-113">La cadena de conexión generada es similar al siguiente código JSON:</span><span class="sxs-lookup"><span data-stu-id="93780-113">The generated connection string is similar to the following JSON:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="93780-115">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="93780-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="93780-116">Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="93780-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="93780-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="93780-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="93780-119">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="93780-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="93780-120">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="93780-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="93780-121">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="93780-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="93780-122">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="93780-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menú Ver](sql/_static/5/ssox.png)

1. <span data-ttu-id="93780-124">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="93780-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Menús contextuales abiertos en la tabla Movie](sql/_static/5/design.png)

   ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

   <span data-ttu-id="93780-127">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="93780-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="93780-128">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="93780-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="93780-129">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="93780-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="93780-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="93780-132">SQLite</span></span>

<span data-ttu-id="93780-133">Según la información del sitio web de [SQLite](https://www.sqlite.org/):</span><span class="sxs-lookup"><span data-stu-id="93780-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="93780-134">SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público.</span><span class="sxs-lookup"><span data-stu-id="93780-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="93780-135">SQLite es el motor de base de datos más usado en el mundo.</span><span class="sxs-lookup"><span data-stu-id="93780-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="93780-136">Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="93780-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="93780-137">La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="93780-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="93780-138">Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.</span><span class="sxs-lookup"><span data-stu-id="93780-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="93780-140">Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="93780-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="93780-141">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="93780-142">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="93780-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="93780-143">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="93780-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="93780-144">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="93780-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="93780-145">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="93780-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="93780-146">En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.</span><span class="sxs-lookup"><span data-stu-id="93780-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="93780-147">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="93780-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="93780-148">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="93780-149">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="93780-149">Creating a new table.</span></span>
>* <span data-ttu-id="93780-150">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="93780-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="93780-151">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="93780-151">Dropping the old table.</span></span>
>* <span data-ttu-id="93780-152">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="93780-152">Renaming the new table.</span></span>
>
><span data-ttu-id="93780-153">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="93780-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="93780-154">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="93780-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="93780-155">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="93780-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="93780-156">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="93780-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="93780-157">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="93780-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="93780-158">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="93780-158">Seed the database</span></span>

<span data-ttu-id="93780-159">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="93780-160">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="93780-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="93780-161">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="93780-161">Add the seed initializer</span></span>

<span data-ttu-id="93780-162">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="93780-163">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="93780-164">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="93780-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="93780-165">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="93780-166">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="93780-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="93780-167">La [instrucción using](/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="93780-167">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="93780-168">La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:</span><span class="sxs-lookup"><span data-stu-id="93780-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="93780-169">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="93780-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="93780-171">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-171">Delete all the records in the database.</span></span> <span data-ttu-id="93780-172">Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="93780-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="93780-173">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="93780-174">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="93780-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="93780-175">Detenga y reinicie IIS con cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="93780-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="93780-176">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y seleccione **Salir** o **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="93780-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menú contextual](sql/_static/stopIIS.png)

   1. <span data-ttu-id="93780-179">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="93780-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="93780-180">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="93780-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-181">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="93780-182">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="93780-183">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="93780-184">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="93780-184">The app shows the seeded data:</span></span>

![Aplicación de películas abierta en el explorador donde se muestran los datos de películas](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="93780-186">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="93780-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="93780-187">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="93780-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="93780-188">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="93780-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="93780-189">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="93780-190">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="93780-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-192">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="93780-193">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="93780-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="93780-194">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93780-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93780-196">La cadena de conexión generada será similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-197">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="93780-198">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="93780-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="93780-199">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="93780-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="93780-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="93780-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="93780-202">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="93780-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="93780-203">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="93780-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="93780-204">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="93780-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="93780-205">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="93780-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="93780-207">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="93780-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menús contextuales abiertos en la tabla Movie](sql/_static/design.png)

  ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

<span data-ttu-id="93780-210">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="93780-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="93780-211">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="93780-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="93780-212">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="93780-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-214">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="93780-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="93780-215">SQLite</span></span>

<span data-ttu-id="93780-216">Según la información del sitio web de [SQLite](https://www.sqlite.org/):</span><span class="sxs-lookup"><span data-stu-id="93780-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="93780-217">SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público.</span><span class="sxs-lookup"><span data-stu-id="93780-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="93780-218">SQLite es el motor de base de datos más usado en el mundo.</span><span class="sxs-lookup"><span data-stu-id="93780-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="93780-219">Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="93780-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="93780-220">La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="93780-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="93780-221">Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.</span><span class="sxs-lookup"><span data-stu-id="93780-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="93780-223">Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="93780-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="93780-224">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="93780-225">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="93780-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="93780-226">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="93780-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="93780-227">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="93780-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="93780-228">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="93780-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="93780-229">En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.</span><span class="sxs-lookup"><span data-stu-id="93780-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="93780-230">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="93780-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="93780-231">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="93780-232">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="93780-232">Creating a new table.</span></span>
>* <span data-ttu-id="93780-233">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="93780-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="93780-234">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="93780-234">Dropping the old table.</span></span>
>* <span data-ttu-id="93780-235">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="93780-235">Renaming the new table.</span></span>
>
><span data-ttu-id="93780-236">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="93780-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="93780-237">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="93780-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="93780-238">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="93780-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="93780-239">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="93780-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="93780-240">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="93780-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="93780-241">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="93780-241">Seed the database</span></span>

<span data-ttu-id="93780-242">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="93780-243">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="93780-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="93780-244">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="93780-244">Add the seed initializer</span></span>

<span data-ttu-id="93780-245">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="93780-246">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="93780-247">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="93780-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="93780-248">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="93780-249">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="93780-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="93780-250">La [instrucción using](/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="93780-250">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="93780-251">La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:</span><span class="sxs-lookup"><span data-stu-id="93780-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="93780-252">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="93780-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="93780-254">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-254">Delete all the records in the database.</span></span> <span data-ttu-id="93780-255">Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="93780-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="93780-256">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="93780-257">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="93780-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="93780-258">Detenga y reinicie IIS con cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="93780-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="93780-259">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="93780-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="93780-262">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="93780-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="93780-263">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="93780-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-264">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="93780-265">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="93780-266">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="93780-267">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="93780-267">The app shows the seeded data:</span></span>

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="93780-269">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="93780-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="93780-270">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="93780-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="93780-271">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="93780-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="93780-272">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="93780-273">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="93780-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="93780-275">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="93780-276">Para más información sobre los modelos empleados en `ConfigureServices`, vea:</span><span class="sxs-lookup"><span data-stu-id="93780-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="93780-277">[Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="93780-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="93780-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="93780-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="93780-279">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="93780-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="93780-280">Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93780-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93780-282">La cadena de conexión generada será similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="93780-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93780-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93780-284">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="93780-285">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción.</span><span class="sxs-lookup"><span data-stu-id="93780-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="93780-286">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="93780-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="93780-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="93780-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="93780-289">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="93780-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="93780-290">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="93780-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="93780-291">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="93780-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="93780-292">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="93780-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="93780-294">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="93780-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menú contextual abierto en la tabla Movie](sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](sql/_static/dv.png)

<span data-ttu-id="93780-297">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="93780-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="93780-298">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="93780-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="93780-299">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="93780-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="93780-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93780-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93780-302">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="93780-303">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="93780-303">Seed the database</span></span>

<span data-ttu-id="93780-304">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="93780-305">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="93780-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="93780-306">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="93780-306">Add the seed initializer</span></span>

<span data-ttu-id="93780-307">Reemplace su contenido por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="93780-308">En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="93780-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="93780-309">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="93780-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="93780-310">Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="93780-311">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="93780-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="93780-312">La [instrucción using](/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.</span><span class="sxs-lookup"><span data-stu-id="93780-312">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="93780-313">Una aplicación de producción no llamaría a `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="93780-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="93780-314">Se agrega al código anterior para evitar que se produzca la siguiente excepción cuando `Update-Database` no se ha ejecutado:</span><span class="sxs-lookup"><span data-stu-id="93780-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="93780-315">SqlException: No se puede abrir la base de datos "RazorPagesMovieContext-21" solicitada por el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="93780-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="93780-316">Error de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="93780-316">The login failed.</span></span>
<span data-ttu-id="93780-317">Error de inicio de sesión del usuario <nombre de usuario>.</span><span class="sxs-lookup"><span data-stu-id="93780-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="93780-318">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="93780-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93780-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93780-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="93780-320">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-320">Delete all the records in the database.</span></span> <span data-ttu-id="93780-321">Puede hacerlo con los vínculos de eliminación en el explorador o desde [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="93780-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="93780-322">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="93780-323">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="93780-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="93780-324">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="93780-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="93780-325">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="93780-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="93780-328">Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="93780-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="93780-329">Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="93780-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93780-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93780-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="93780-331">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="93780-332">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93780-333">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="93780-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93780-334">Elimine todos los registros de la base de datos para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="93780-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="93780-335">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="93780-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="93780-336">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="93780-336">The app shows the seeded data:</span></span>

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

<span data-ttu-id="93780-338">El siguiente tutorial limpia la presentación de los datos.</span><span class="sxs-lookup"><span data-stu-id="93780-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93780-339">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="93780-339">Additional resources</span></span>

* [<span data-ttu-id="93780-340">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="93780-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="93780-341">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="93780-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end