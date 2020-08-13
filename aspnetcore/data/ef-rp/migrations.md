---
title: 'Parte 4. Razor Pages con EF Core en ASP.NET Core: Migraciones'
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/migrations
ms.openlocfilehash: ebdcf5605e8b4067c52841b138184e51668f9c25
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019656"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="ff42d-103">Parte 4. Razor Pages con migraciones de EF Core en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff42d-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="ff42d-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ff42d-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff42d-105">En este tutorial se presenta la característica de migraciones de EF Core para administrar cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="ff42d-106">Cuando se desarrolla una aplicación nueva, el modelo de datos cambia con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="ff42d-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="ff42d-107">Cada vez que el modelo cambia, este deja de estar sincronizado con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="ff42d-108">Esta serie de tutoriales se ha iniciado con la configuración de Entity Framework para crear la base de datos si no existe.</span><span class="sxs-lookup"><span data-stu-id="ff42d-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="ff42d-109">Cada vez que cambia el modelo de datos, tiene que quitar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="ff42d-110">La próxima vez que se ejecute la aplicación, la llamada a `EnsureCreated` vuelve a crear la base de datos para que coincida con el nuevo modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="ff42d-111">Después, se ejecuta la clase `DbInitializer` para inicializar la nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="ff42d-112">Este enfoque para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción.</span><span class="sxs-lookup"><span data-stu-id="ff42d-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="ff42d-113">Cuando se ejecuta la aplicación en producción, normalmente está almacenando datos que hay que mantener.</span><span class="sxs-lookup"><span data-stu-id="ff42d-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="ff42d-114">No se puede iniciar la aplicación con una base de datos de prueba cada vez que se realiza un cambio (por ejemplo, agregar una columna nueva).</span><span class="sxs-lookup"><span data-stu-id="ff42d-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="ff42d-115">La característica Migraciones de EF Core soluciona este problema permitiendo a EF Core actualizar el esquema de la base de datos en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="ff42d-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="ff42d-116">En lugar de quitar y volver a crear la base de datos cuando cambian los datos del modelo, las migraciones actualizan el esquema y conservan los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="ff42d-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="ff42d-117">Eliminación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff42d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff42d-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff42d-119">Use **Explorador de objetos de SQL Server** (SSOX) para eliminar la base de datos, o bien ejecute el comando siguiente en la **Consola del administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="ff42d-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff42d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff42d-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ff42d-121">Ejecute el comando siguiente en un símbolo del sistema para instalar la CLI de EF:</span><span class="sxs-lookup"><span data-stu-id="ff42d-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="ff42d-122">En el símbolo del sistema, vaya a la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ff42d-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="ff42d-123">La carpeta del proyecto contiene el archivo *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="ff42d-124">Elimine el archivo *CU.db*, o bien ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="ff42d-125">Crear una migración inicial</span><span class="sxs-lookup"><span data-stu-id="ff42d-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff42d-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff42d-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff42d-127">Ejecute los comandos siguientes en la Consola del administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="ff42d-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff42d-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff42d-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ff42d-129">Asegúrese de que el símbolo del sistema está en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ff42d-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="ff42d-130">Métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="ff42d-130">Up and Down methods</span></span>

<span data-ttu-id="ff42d-131">El comando `migrations add` de EF Core ha generado código para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="ff42d-132">Este código de migraciones se encuentra en el archivo *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="ff42d-133">El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que se corresponden a los conjuntos de entidades del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="ff42d-134">El método `Down` las elimina, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="ff42d-135">El código anterior es para la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="ff42d-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="ff42d-136">El código:</span><span class="sxs-lookup"><span data-stu-id="ff42d-136">The code:</span></span>

* <span data-ttu-id="ff42d-137">Lo ha generado el comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="ff42d-138">Lo ejecuta el comando `database update`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="ff42d-139">Crea una base de datos para el modelo de datos especificado por la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="ff42d-140">El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="ff42d-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="ff42d-141">El nombre de la migración puede ser cualquier nombre de archivo válido.</span><span class="sxs-lookup"><span data-stu-id="ff42d-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="ff42d-142">Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="ff42d-143">Por ejemplo, una migración que ha agregado una tabla de departamento podría denominarse "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="ff42d-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="ff42d-144">La tabla de historial de migraciones</span><span class="sxs-lookup"><span data-stu-id="ff42d-144">The migrations history table</span></span>

* <span data-ttu-id="ff42d-145">Use SSOX o la herramienta SQLite para inspeccionar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="ff42d-146">Observe la adición de una tabla `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ff42d-147">En la tabla `__EFMigrationsHistory` se realiza el seguimiento de las migraciones que se han aplicado a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="ff42d-148">Vea los datos de la tabla `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ff42d-149">Muestra una fila para la primera migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="ff42d-150">La instantánea del modelo de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-150">The data model snapshot</span></span>

<span data-ttu-id="ff42d-151">Las migraciones crean una *instantánea* del modelo de datos actual en *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="ff42d-152">Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos actual con el archivo de instantánea.</span><span class="sxs-lookup"><span data-stu-id="ff42d-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="ff42d-153">Como el archivo de instantánea realiza el seguimiento del estado del modelo de datos, no se puede eliminar una migración mediante la eliminación del archivo `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="ff42d-154">Para quitar la migración más reciente, tiene que usar el comando `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="ff42d-155">Ese comando elimina la migración y garantiza que la instantánea se restablezca de forma correcta.</span><span class="sxs-lookup"><span data-stu-id="ff42d-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="ff42d-156">Para obtener más información, vea [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="ff42d-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="ff42d-157">Quitar EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="ff42d-157">Remove EnsureCreated</span></span>

<span data-ttu-id="ff42d-158">Esta serie de tutoriales se ha iniciado con `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="ff42d-159">`EnsureCreated` no crea una tabla de historial de migraciones y, por tanto, no se puede usar con las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="ff42d-160">Está diseñado para crear prototipos rápidos o de prueba donde la base de datos se quita y se vuelve a crear con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="ff42d-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="ff42d-161">A partir de este punto, en los tutoriales se usarán las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="ff42d-162">En *Data/DBInitializer.cs*, comente la línea siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="ff42d-163">Ejecute la aplicación y compruebe que la base de datos se ha inicializado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="ff42d-164">Aplicar las migraciones en producción</span><span class="sxs-lookup"><span data-stu-id="ff42d-164">Applying migrations in production</span></span>

<span data-ttu-id="ff42d-165">Se recomienda que las aplicaciones de producción **no** llamen a [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ff42d-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="ff42d-166">`Migrate` no se debe llamar desde una aplicación que se implementa en una granja de servidores.</span><span class="sxs-lookup"><span data-stu-id="ff42d-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="ff42d-167">Si la aplicación se escala horizontalmente en varias instancias de servidor, es difícil garantizar que las actualizaciones del esquema de la base de datos no se realicen en varios servidores o que estén en conflicto con el acceso de lectura y escritura.</span><span class="sxs-lookup"><span data-stu-id="ff42d-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="ff42d-168">La migración de bases de datos debe realizarse como parte de la implementación y de un modo controlado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="ff42d-169">Entre los métodos de migración de base de datos de producción se incluyen:</span><span class="sxs-lookup"><span data-stu-id="ff42d-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="ff42d-170">Uso de las migraciones para crear scripts SQL y uso de scripts SQL en la implementación.</span><span class="sxs-lookup"><span data-stu-id="ff42d-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="ff42d-171">Ejecución de `dotnet ef database update` desde un entorno controlado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ff42d-172">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="ff42d-172">Troubleshooting</span></span>

<span data-ttu-id="ff42d-173">Si en la aplicación se usa SQL Server LocalDB y se muestra la excepción siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="ff42d-174">La solución puede ser ejecutar `dotnet ef database update` en un símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="ff42d-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="ff42d-175">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ff42d-175">Additional resources</span></span>

* <span data-ttu-id="ff42d-176">[CLI de EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="ff42d-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="ff42d-177">Consola del administrador de paquetes (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="ff42d-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="ff42d-178">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="ff42d-178">Next steps</span></span>

<span data-ttu-id="ff42d-179">En el tutorial siguiente se crea el modelo de datos y se agregan propiedades de entidad y entidades nuevas.</span><span class="sxs-lookup"><span data-stu-id="ff42d-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ff42d-180">[Tutorial anterior](xref:data/ef-rp/sort-filter-page)
> [Tutorial siguiente](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="ff42d-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff42d-181">En este tutorial, se usa la característica de migraciones de EF Core para administrar cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="ff42d-182">Si experimenta problemas que no puede resolver, descargue la [aplicación completada](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ff42d-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="ff42d-183">Cuando se desarrolla una aplicación nueva, el modelo de datos cambia con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="ff42d-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="ff42d-184">Cada vez que el modelo cambia, este deja de estar sincronizado con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="ff42d-185">Este tutorial se inició con la configuración de Entity Framework para crear la base de datos si no existía.</span><span class="sxs-lookup"><span data-stu-id="ff42d-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="ff42d-186">Cada vez que los datos del modelo cambian:</span><span class="sxs-lookup"><span data-stu-id="ff42d-186">Each time the data model changes:</span></span>

* <span data-ttu-id="ff42d-187">Se quita la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-187">The DB is dropped.</span></span>
* <span data-ttu-id="ff42d-188">EF crea una que coincide con el modelo.</span><span class="sxs-lookup"><span data-stu-id="ff42d-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="ff42d-189">La aplicación inicializa la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="ff42d-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="ff42d-190">Este enfoque para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción.</span><span class="sxs-lookup"><span data-stu-id="ff42d-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="ff42d-191">Cuando se ejecuta la aplicación en producción, normalmente está almacenando datos que hay que mantener.</span><span class="sxs-lookup"><span data-stu-id="ff42d-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="ff42d-192">No se puede iniciar la aplicación con una prueba de base de datos cada vez que se hace un cambio (por ejemplo, agregar una nueva columna).</span><span class="sxs-lookup"><span data-stu-id="ff42d-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="ff42d-193">La característica Migraciones de EF Core soluciona este problema habilitando EF Core para actualizar el esquema de la base de datos en lugar de crear una.</span><span class="sxs-lookup"><span data-stu-id="ff42d-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="ff42d-194">En lugar de quitar y volver a crear la base de datos cuando los datos del modelo cambian, las migraciones actualizan el esquema y conservan los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="ff42d-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="ff42d-195">Eliminación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-195">Drop the database</span></span>

<span data-ttu-id="ff42d-196">Use el **Explorador de objetos de SQL Server** (SSOX) o el comando `database drop`:</span><span class="sxs-lookup"><span data-stu-id="ff42d-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff42d-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff42d-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff42d-198">En la **Consola del Administrador de paquetes** (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="ff42d-199">Ejecute `Get-Help about_EntityFrameworkCore` desde PMC para obtener información de ayuda.</span><span class="sxs-lookup"><span data-stu-id="ff42d-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff42d-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff42d-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ff42d-201">Abra una ventana de comandos y desplácese hasta la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ff42d-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ff42d-202">La carpeta del proyecto contiene el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="ff42d-203">Escriba lo siguiente en la ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="ff42d-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="ff42d-204">Creación de una migración inicial y actualización de la base de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="ff42d-205">Compile el proyecto y cree la primera migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff42d-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff42d-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff42d-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff42d-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="ff42d-208">Examinar los métodos Up y Down</span><span class="sxs-lookup"><span data-stu-id="ff42d-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="ff42d-209">El comando `migrations add` de EF Core ha generado código para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="ff42d-210">Este código de migraciones se encuentra en el archivo *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="ff42d-211">El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que corresponden a los conjuntos de entidades del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="ff42d-212">El método `Down` las elimina, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="ff42d-213">Las migraciones llaman al método `Up` para implementar los cambios del modelo de datos para una migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="ff42d-214">Cuando se escribe un comando para revertir la actualización, las migraciones llaman al método `Down`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="ff42d-215">El código anterior es para la migración inicial.</span><span class="sxs-lookup"><span data-stu-id="ff42d-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="ff42d-216">Ese código se creó cuando se ejecutó el comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="ff42d-217">El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="ff42d-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="ff42d-218">El nombre de la migración puede ser cualquier nombre de archivo válido.</span><span class="sxs-lookup"><span data-stu-id="ff42d-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="ff42d-219">Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="ff42d-220">Por ejemplo, una migración que ha agregado una tabla de departamento podría denominarse "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="ff42d-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="ff42d-221">Si la migración inicial está creada y la base de datos existe:</span><span class="sxs-lookup"><span data-stu-id="ff42d-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="ff42d-222">Se genera el código de creación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="ff42d-223">El código de creación de la base de datos no tiene que ejecutarse porque la base de datos ya coincide con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="ff42d-224">Si el código de creación de la base de datos se está ejecutando, no hace ningún cambio porque la base de datos ya coincide con el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="ff42d-225">Cuando la aplicación se implementa en un entorno nuevo, se debe ejecutar el código de creación de la base de datos para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="ff42d-226">Anteriormente, la base de datos se eliminó, de modo que ya no existe y ahora se crea mediante las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="ff42d-227">La instantánea del modelo de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-227">The data model snapshot</span></span>

<span data-ttu-id="ff42d-228">Las migraciones crean una *instantánea* del esquema de la base de datos actual en *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="ff42d-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="ff42d-229">Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos con el archivo de instantánea.</span><span class="sxs-lookup"><span data-stu-id="ff42d-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="ff42d-230">Para eliminar una migración, use el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="ff42d-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff42d-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff42d-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff42d-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="ff42d-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff42d-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff42d-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="ff42d-234">Para obtener más información, vea [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="ff42d-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="ff42d-235">El comando remove migrations elimina la migración y garantiza que la instantánea se restablece correctamente.</span><span class="sxs-lookup"><span data-stu-id="ff42d-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="ff42d-236">Eliminación de EnsureCreated y prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="ff42d-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="ff42d-237">Para el desarrollo inicial se ha utilizado `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="ff42d-238">En este tutorial, se usan las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="ff42d-239">`EnsureCreated` tiene las siguientes limitaciones:</span><span class="sxs-lookup"><span data-stu-id="ff42d-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="ff42d-240">Omite las migraciones y crea la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="ff42d-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="ff42d-241">No crea una tabla de migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="ff42d-242">*No* puede usarse con las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="ff42d-243">Está diseñado para crear prototipos rápidos o de prueba donde se quita y vuelve a crear la base de datos con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="ff42d-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="ff42d-244">Quite `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="ff42d-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="ff42d-245">Ejecute la aplicación y compruebe que la base de datos se haya inicializado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="ff42d-246">Inspección de la base de datos</span><span class="sxs-lookup"><span data-stu-id="ff42d-246">Inspect the database</span></span>

<span data-ttu-id="ff42d-247">Use el **Explorador de objetos de SQL Server** para inspeccionar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="ff42d-248">Observe la adición de una tabla `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="ff42d-249">La tabla `__EFMigrationsHistory` realiza un seguimiento de las migraciones que se han aplicado a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ff42d-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="ff42d-250">Examine los datos de la tabla `__EFMigrationsHistory`, muestra una fila para la primera migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="ff42d-251">En el último registro del ejemplo de salida de la CLI anterior se muestra la instrucción INSERT que crea esta fila.</span><span class="sxs-lookup"><span data-stu-id="ff42d-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="ff42d-252">Ejecute la aplicación y compruebe que todo funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="ff42d-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="ff42d-253">Aplicar las migraciones en producción</span><span class="sxs-lookup"><span data-stu-id="ff42d-253">Applying migrations in production</span></span>

<span data-ttu-id="ff42d-254">Se recomienda que las aplicaciones de producción **no** llamen a [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ff42d-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="ff42d-255">No debe llamarse a `Migrate` desde una aplicación en la granja de servidores.</span><span class="sxs-lookup"><span data-stu-id="ff42d-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="ff42d-256">Por ejemplo, si la aplicación se ha implementado en la nube con escalado horizontal (se ejecutan varias instancias de la aplicación).</span><span class="sxs-lookup"><span data-stu-id="ff42d-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="ff42d-257">La migración de bases de datos debe realizarse como parte de la implementación y de un modo controlado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="ff42d-258">Entre los métodos de migración de base de datos de producción se incluyen:</span><span class="sxs-lookup"><span data-stu-id="ff42d-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="ff42d-259">Uso de las migraciones para crear scripts SQL y uso de scripts SQL en la implementación.</span><span class="sxs-lookup"><span data-stu-id="ff42d-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="ff42d-260">Ejecución de `dotnet ef database update` desde un entorno controlado.</span><span class="sxs-lookup"><span data-stu-id="ff42d-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="ff42d-261">EF Core usa la tabla `__MigrationsHistory` para ver si es necesario ejecutar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="ff42d-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="ff42d-262">Si la base de datos está actualizada, no se ejecuta ninguna migración.</span><span class="sxs-lookup"><span data-stu-id="ff42d-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ff42d-263">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="ff42d-263">Troubleshooting</span></span>

<span data-ttu-id="ff42d-264">Descargue la [aplicación completada](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="ff42d-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="ff42d-265">La aplicación genera la siguiente excepción:</span><span class="sxs-lookup"><span data-stu-id="ff42d-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="ff42d-266">Solución: Ejecute `dotnet ef database update`.</span><span class="sxs-lookup"><span data-stu-id="ff42d-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="ff42d-267">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ff42d-267">Additional resources</span></span>

* [<span data-ttu-id="ff42d-268">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="ff42d-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="ff42d-269">[CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="ff42d-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="ff42d-270">Consola del administrador de paquetes (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="ff42d-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="ff42d-271">[Anterior](xref:data/ef-rp/sort-filter-page)
> [Siguiente](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="ff42d-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

