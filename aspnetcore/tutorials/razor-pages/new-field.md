---
title: Parte 7, adición de un campo nuevo
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: cd637507e19735f020b4c28e6f22de0e7e772040
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588326"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="3a229-103">Parte 7. Adición de un campo nuevo a una instancia de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a229-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="3a229-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3a229-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3a229-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3a229-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3a229-106">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="3a229-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3a229-107">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-107">Add a new field to the model.</span></span>
* <span data-ttu-id="3a229-108">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3a229-109">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3a229-110">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="3a229-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3a229-111">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="3a229-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3a229-112">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="3a229-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3a229-113">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="3a229-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="3a229-114">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="3a229-115">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-115">Build the app.</span></span>

1. <span data-ttu-id="3a229-116">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="3a229-117">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="3a229-117">Update the following pages:</span></span>
   1. <span data-ttu-id="3a229-118">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="3a229-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="3a229-119">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="3a229-120">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="3a229-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3a229-121">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="3a229-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3a229-122">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="3a229-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3a229-123">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3a229-124">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3a229-125">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="3a229-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3a229-126">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3a229-127">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3a229-128">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3a229-129">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="3a229-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="3a229-130">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3a229-131">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3a229-132">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3a229-133">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3a229-134">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3a229-135">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="3a229-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3a229-136">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="3a229-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3a229-137">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3a229-138">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="3a229-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3a229-139">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="3a229-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a229-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a229-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3a229-141">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="3a229-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="3a229-142">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="3a229-143">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="3a229-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="3a229-144">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="3a229-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3a229-145">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3a229-146">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3a229-147">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3a229-148">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3a229-149">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="3a229-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3a229-150">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3a229-151">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3a229-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3a229-152">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3a229-153">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="3a229-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="3a229-154">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="3a229-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="3a229-155">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="3a229-156">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="3a229-157">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-157">Select **OK**.</span></span>
1. <span data-ttu-id="3a229-158">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="3a229-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3a229-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3a229-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3a229-160">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="3a229-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3a229-161">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="3a229-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3a229-162">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3a229-163">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="3a229-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3a229-164">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="3a229-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3a229-165">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="3a229-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3a229-166">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="3a229-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3a229-167">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="3a229-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3a229-168">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="3a229-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3a229-169">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3a229-170">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-170">Creating a new table.</span></span>
>* <span data-ttu-id="3a229-171">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3a229-172">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="3a229-172">Dropping the old table.</span></span>
>* <span data-ttu-id="3a229-173">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="3a229-173">Renaming the new table.</span></span>
>
><span data-ttu-id="3a229-174">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="3a229-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3a229-175">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="3a229-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3a229-176">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="3a229-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3a229-177">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="3a229-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3a229-178">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="3a229-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="3a229-179">Elimine la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="3a229-180">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="3a229-181">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3a229-182">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="3a229-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a229-183">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3a229-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3a229-184">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3a229-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="3a229-185">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3a229-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3a229-186">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="3a229-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3a229-187">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-187">Add a new field to the model.</span></span>
* <span data-ttu-id="3a229-188">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3a229-189">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3a229-190">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="3a229-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3a229-191">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="3a229-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3a229-192">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="3a229-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3a229-193">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="3a229-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="3a229-194">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="3a229-195">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-195">Build the app.</span></span>

1. <span data-ttu-id="3a229-196">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="3a229-197">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="3a229-197">Update the following pages:</span></span>
   1. <span data-ttu-id="3a229-198">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="3a229-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="3a229-199">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="3a229-200">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="3a229-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3a229-201">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="3a229-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3a229-202">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="3a229-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3a229-203">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3a229-204">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3a229-205">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="3a229-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3a229-206">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3a229-207">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3a229-208">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3a229-209">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="3a229-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="3a229-210">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3a229-211">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3a229-212">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3a229-213">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3a229-214">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3a229-215">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="3a229-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3a229-216">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="3a229-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3a229-217">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3a229-218">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="3a229-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3a229-219">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="3a229-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a229-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a229-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3a229-221">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="3a229-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="3a229-222">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="3a229-223">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="3a229-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="3a229-224">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="3a229-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3a229-225">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3a229-226">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3a229-227">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3a229-228">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3a229-229">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="3a229-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3a229-230">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3a229-231">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3a229-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3a229-232">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3a229-233">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="3a229-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="3a229-234">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="3a229-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="3a229-235">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="3a229-236">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="3a229-237">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-237">Select **OK**.</span></span>
* <span data-ttu-id="3a229-238">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="3a229-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3a229-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3a229-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3a229-240">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="3a229-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3a229-241">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="3a229-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3a229-242">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3a229-243">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="3a229-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3a229-244">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="3a229-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3a229-245">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="3a229-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3a229-246">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="3a229-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3a229-247">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="3a229-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3a229-248">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="3a229-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3a229-249">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3a229-250">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-250">Creating a new table.</span></span>
>* <span data-ttu-id="3a229-251">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3a229-252">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="3a229-252">Dropping the old table.</span></span>
>* <span data-ttu-id="3a229-253">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="3a229-253">Renaming the new table.</span></span>
>
><span data-ttu-id="3a229-254">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="3a229-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3a229-255">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="3a229-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3a229-256">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="3a229-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3a229-257">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="3a229-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3a229-258">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="3a229-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="3a229-259">Elimine la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="3a229-260">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="3a229-261">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3a229-262">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="3a229-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a229-263">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3a229-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3a229-264">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3a229-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a229-265">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3a229-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3a229-266">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="3a229-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3a229-267">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-267">Add a new field to the model.</span></span>
* <span data-ttu-id="3a229-268">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3a229-269">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3a229-270">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="3a229-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3a229-271">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="3a229-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3a229-272">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="3a229-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3a229-273">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="3a229-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="3a229-274">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="3a229-275">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-275">Build the app.</span></span>

<span data-ttu-id="3a229-276">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="3a229-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="3a229-277">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="3a229-277">Update the following pages:</span></span>

* <span data-ttu-id="3a229-278">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="3a229-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="3a229-279">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="3a229-280">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="3a229-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3a229-281">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="3a229-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3a229-282">Si la aplicación se ejecuta ahora, la aplicación produce `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="3a229-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3a229-283">Este error se debe a que la clase del modelo Movie actualizado es diferente al esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3a229-284">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3a229-285">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="3a229-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3a229-286">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3a229-287">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3a229-288">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3a229-289">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="3a229-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="3a229-290">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3a229-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3a229-291">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3a229-292">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3a229-293">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3a229-294">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3a229-295">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="3a229-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3a229-296">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="3a229-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3a229-297">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3a229-298">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="3a229-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3a229-299">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="3a229-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a229-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a229-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3a229-301">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="3a229-301">Add a migration for the rating field</span></span>

<span data-ttu-id="3a229-302">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="3a229-303">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="3a229-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="3a229-304">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="3a229-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3a229-305">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3a229-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3a229-306">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="3a229-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3a229-307">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3a229-308">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="3a229-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3a229-309">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3a229-310">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3a229-311">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="3a229-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3a229-312">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3a229-313">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="3a229-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="3a229-314">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="3a229-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="3a229-315">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="3a229-316">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="3a229-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="3a229-317">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3a229-317">Select **OK**.</span></span>
* <span data-ttu-id="3a229-318">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="3a229-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3a229-319">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3a229-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3a229-320">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="3a229-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3a229-321">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="3a229-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3a229-322">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3a229-323">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="3a229-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3a229-324">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="3a229-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3a229-325">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="3a229-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3a229-326">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="3a229-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3a229-327">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="3a229-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3a229-328">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="3a229-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3a229-329">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3a229-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3a229-330">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-330">Creating a new table.</span></span>
>* <span data-ttu-id="3a229-331">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="3a229-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3a229-332">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="3a229-332">Dropping the old table.</span></span>
>* <span data-ttu-id="3a229-333">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="3a229-333">Renaming the new table.</span></span>
>
><span data-ttu-id="3a229-334">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="3a229-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3a229-335">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="3a229-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3a229-336">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="3a229-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3a229-337">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="3a229-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3a229-338">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="3a229-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="3a229-339">Elimine la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3a229-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3a229-340">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="3a229-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="3a229-341">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="3a229-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="3a229-342">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="3a229-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3a229-343">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="3a229-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a229-344">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3a229-344">Additional resources</span></span>

* [<span data-ttu-id="3a229-345">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="3a229-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="3a229-346">[Anterior: Adición de búsqueda ](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3a229-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end