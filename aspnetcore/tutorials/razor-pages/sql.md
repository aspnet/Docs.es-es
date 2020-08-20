---
title: Parte 4. Trabajo con una base de datos y ASP.NET Core
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 7/22/2019
no-loc:
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
ms.openlocfilehash: d2e18782411b1801c74fa33ba1b31bad9662f3b2
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627122"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="f6af7-103">Parte 4. Trabajo con una base de datos y ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6af7-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="f6af7-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="f6af7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f6af7-105">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f6af7-106">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6af7-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-108">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="f6af7-109">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f6af7-110">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6af7-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6af7-112">El valor de nombre de la base de datos (`Database={Database name}`) será distinto en su código generado.</span><span class="sxs-lookup"><span data-stu-id="f6af7-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="f6af7-113">El valor de nombre es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="f6af7-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="f6af7-115">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos real.</span><span class="sxs-lookup"><span data-stu-id="f6af7-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="f6af7-116">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f6af7-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f6af7-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f6af7-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f6af7-119">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="f6af7-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="f6af7-120">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="f6af7-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f6af7-121">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="f6af7-122">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f6af7-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="f6af7-124">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menús contextuales abiertos en la tabla Movie](sql/_static/design.png)

  ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

<span data-ttu-id="f6af7-127">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f6af7-128">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="f6af7-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="f6af7-129">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="f6af7-132">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="f6af7-132">Seed the database</span></span>

<span data-ttu-id="f6af7-133">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6af7-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f6af7-134">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="f6af7-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f6af7-135">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="f6af7-135">Add the seed initializer</span></span>

<span data-ttu-id="f6af7-136">En *Program.cs*, modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6af7-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f6af7-137">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f6af7-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f6af7-138">Llamar al método de inicialización, pasándolo al contexto.</span><span class="sxs-lookup"><span data-stu-id="f6af7-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="f6af7-139">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="f6af7-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="f6af7-140">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="f6af7-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="f6af7-141">La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:</span><span class="sxs-lookup"><span data-stu-id="f6af7-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="f6af7-142">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f6af7-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6af7-144">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-144">Delete all the records in the DB.</span></span> <span data-ttu-id="f6af7-145">Puede hacerlo con los vínculos de eliminación en el explorador o desde [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="f6af7-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="f6af7-146">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="f6af7-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f6af7-147">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f6af7-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f6af7-148">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="f6af7-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f6af7-149">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="f6af7-152">Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6af7-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="f6af7-153">Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5.</span><span class="sxs-lookup"><span data-stu-id="f6af7-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-154">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6af7-155">Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización).</span><span class="sxs-lookup"><span data-stu-id="f6af7-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f6af7-156">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f6af7-157">La aplicación muestra los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="f6af7-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="f6af7-158">En el tutorial siguiente se mejorará la presentación de los datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6af7-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f6af7-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f6af7-160">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="f6af7-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f6af7-161">El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f6af7-162">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6af7-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="f6af7-165">Para más información sobre los modelos empleados en `ConfigureServices`, vea:</span><span class="sxs-lookup"><span data-stu-id="f6af7-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="f6af7-166">[Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="f6af7-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="f6af7-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="f6af7-168">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f6af7-169">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6af7-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6af7-171">El valor de nombre de la base de datos (`Database={Database name}`) será distinto en su código generado.</span><span class="sxs-lookup"><span data-stu-id="f6af7-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="f6af7-172">El valor de nombre es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="f6af7-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6af7-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6af7-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-174">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="f6af7-175">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos real.</span><span class="sxs-lookup"><span data-stu-id="f6af7-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="f6af7-176">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f6af7-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f6af7-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f6af7-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f6af7-179">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="f6af7-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="f6af7-180">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="f6af7-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f6af7-181">De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="f6af7-182">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f6af7-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](sql/_static/ssox.png)

* <span data-ttu-id="f6af7-184">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menú contextual abierto en la tabla Movie](sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](sql/_static/dv.png)

<span data-ttu-id="f6af7-187">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f6af7-188">De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.</span><span class="sxs-lookup"><span data-stu-id="f6af7-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="f6af7-189">Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6af7-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6af7-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-192">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="f6af7-193">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="f6af7-193">Seed the database</span></span>

<span data-ttu-id="f6af7-194">Cree una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6af7-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f6af7-195">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="f6af7-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f6af7-196">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="f6af7-196">Add the seed initializer</span></span>

<span data-ttu-id="f6af7-197">En *Program.cs*, modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6af7-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f6af7-198">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f6af7-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f6af7-199">Llamar al método de inicialización, pasándolo al contexto.</span><span class="sxs-lookup"><span data-stu-id="f6af7-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="f6af7-200">Eliminar el contexto cuando el método de inicialización finalice.</span><span class="sxs-lookup"><span data-stu-id="f6af7-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="f6af7-201">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="f6af7-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="f6af7-202">Una aplicación de producción no llamaría a `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="f6af7-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="f6af7-203">Se agrega al código anterior para evitar que se produzca la siguiente excepción cuando `Update-Database` no se ha ejecutado:</span><span class="sxs-lookup"><span data-stu-id="f6af7-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="f6af7-204">SqlException: No se puede abrir la base de datos "RazorPagesMovieContext-21" solicitada por el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f6af7-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="f6af7-205">Error de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f6af7-205">The login failed.</span></span>
<span data-ttu-id="f6af7-206">Error de inicio de sesión del usuario <nombre de usuario>.</span><span class="sxs-lookup"><span data-stu-id="f6af7-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f6af7-207">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f6af7-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6af7-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6af7-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6af7-209">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-209">Delete all the records in the DB.</span></span> <span data-ttu-id="f6af7-210">Puede hacerlo con los vínculos de eliminación en el explorador o desde [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="f6af7-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="f6af7-211">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="f6af7-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f6af7-212">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f6af7-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f6af7-213">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="f6af7-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f6af7-214">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:</span><span class="sxs-lookup"><span data-stu-id="f6af7-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="f6af7-217">Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración.</span><span class="sxs-lookup"><span data-stu-id="f6af7-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="f6af7-218">Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5.</span><span class="sxs-lookup"><span data-stu-id="f6af7-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6af7-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6af7-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6af7-220">Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización).</span><span class="sxs-lookup"><span data-stu-id="f6af7-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f6af7-221">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f6af7-222">La aplicación muestra los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="f6af7-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6af7-223">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f6af7-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6af7-224">Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización).</span><span class="sxs-lookup"><span data-stu-id="f6af7-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f6af7-225">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="f6af7-226">La aplicación muestra los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="f6af7-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="f6af7-227">La aplicación muestra los datos inicializados:</span><span class="sxs-lookup"><span data-stu-id="f6af7-227">The app shows the seeded data:</span></span>

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55.png)

<span data-ttu-id="f6af7-229">El siguiente tutorial limpia la presentación de los datos.</span><span class="sxs-lookup"><span data-stu-id="f6af7-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6af7-230">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f6af7-230">Additional resources</span></span>

* [<span data-ttu-id="f6af7-231">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="f6af7-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="f6af7-232">[Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="f6af7-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
