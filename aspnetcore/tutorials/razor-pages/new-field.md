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
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101151"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="18559-103">Parte 7. Adición de un campo nuevo a una instancia de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18559-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="18559-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18559-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="18559-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18559-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="18559-106">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="18559-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="18559-107">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-107">Add a new field to the model.</span></span>
* <span data-ttu-id="18559-108">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="18559-109">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="18559-110">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="18559-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="18559-111">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="18559-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="18559-112">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="18559-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="18559-113">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="18559-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="18559-114">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="18559-115">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-115">Build the app.</span></span>

1. <span data-ttu-id="18559-116">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="18559-117">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="18559-117">Update the following pages:</span></span>
   1. <span data-ttu-id="18559-118">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="18559-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="18559-119">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="18559-120">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="18559-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="18559-121">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="18559-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="18559-122">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="18559-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="18559-123">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="18559-124">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="18559-125">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="18559-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="18559-126">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="18559-127">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="18559-128">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="18559-129">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="18559-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="18559-130">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="18559-131">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="18559-132">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="18559-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="18559-133">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="18559-134">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="18559-135">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="18559-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="18559-136">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="18559-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="18559-137">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="18559-138">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="18559-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="18559-139">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="18559-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18559-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18559-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="18559-141">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="18559-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="18559-142">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="18559-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="18559-143">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="18559-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="18559-144">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="18559-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="18559-145">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="18559-146">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="18559-147">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="18559-148">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="18559-149">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="18559-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="18559-150">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="18559-151">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="18559-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="18559-152">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="18559-153">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="18559-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="18559-154">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="18559-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="18559-155">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="18559-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="18559-156">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="18559-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="18559-157">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="18559-157">Select **OK**.</span></span>
1. <span data-ttu-id="18559-158">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="18559-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="18559-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="18559-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="18559-160">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="18559-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="18559-161">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="18559-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="18559-162">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="18559-163">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="18559-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="18559-164">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="18559-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="18559-165">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="18559-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="18559-166">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="18559-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="18559-167">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="18559-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="18559-168">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="18559-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="18559-169">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="18559-170">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-170">Creating a new table.</span></span>
>* <span data-ttu-id="18559-171">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="18559-172">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="18559-172">Dropping the old table.</span></span>
>* <span data-ttu-id="18559-173">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="18559-173">Renaming the new table.</span></span>
>
><span data-ttu-id="18559-174">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="18559-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="18559-175">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="18559-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="18559-176">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="18559-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="18559-177">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="18559-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="18559-178">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="18559-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="18559-179">Elimine la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="18559-180">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="18559-181">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="18559-182">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="18559-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18559-183">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="18559-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="18559-184">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="18559-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="18559-185">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18559-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="18559-186">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="18559-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="18559-187">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-187">Add a new field to the model.</span></span>
* <span data-ttu-id="18559-188">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="18559-189">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="18559-190">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="18559-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="18559-191">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="18559-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="18559-192">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="18559-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="18559-193">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="18559-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="18559-194">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="18559-195">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-195">Build the app.</span></span>

1. <span data-ttu-id="18559-196">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="18559-197">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="18559-197">Update the following pages:</span></span>
   1. <span data-ttu-id="18559-198">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="18559-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="18559-199">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="18559-200">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="18559-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="18559-201">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="18559-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="18559-202">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="18559-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="18559-203">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="18559-204">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="18559-205">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="18559-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="18559-206">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="18559-207">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="18559-208">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="18559-209">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="18559-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="18559-210">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="18559-211">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="18559-212">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="18559-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="18559-213">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="18559-214">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="18559-215">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="18559-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="18559-216">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="18559-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="18559-217">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="18559-218">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="18559-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="18559-219">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="18559-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18559-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18559-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="18559-221">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="18559-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="18559-222">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="18559-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="18559-223">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="18559-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="18559-224">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="18559-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="18559-225">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="18559-226">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="18559-227">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="18559-228">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="18559-229">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="18559-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="18559-230">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="18559-231">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="18559-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="18559-232">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="18559-233">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="18559-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="18559-234">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="18559-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="18559-235">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="18559-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="18559-236">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="18559-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="18559-237">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="18559-237">Select **OK**.</span></span>
* <span data-ttu-id="18559-238">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="18559-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="18559-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="18559-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="18559-240">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="18559-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="18559-241">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="18559-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="18559-242">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="18559-243">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="18559-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="18559-244">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="18559-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="18559-245">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="18559-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="18559-246">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="18559-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="18559-247">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="18559-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="18559-248">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="18559-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="18559-249">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="18559-250">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-250">Creating a new table.</span></span>
>* <span data-ttu-id="18559-251">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="18559-252">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="18559-252">Dropping the old table.</span></span>
>* <span data-ttu-id="18559-253">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="18559-253">Renaming the new table.</span></span>
>
><span data-ttu-id="18559-254">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="18559-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="18559-255">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="18559-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="18559-256">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="18559-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="18559-257">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="18559-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="18559-258">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="18559-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="18559-259">Elimine la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="18559-260">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="18559-261">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="18559-262">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="18559-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18559-263">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="18559-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="18559-264">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="18559-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18559-265">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18559-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="18559-266">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="18559-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="18559-267">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-267">Add a new field to the model.</span></span>
* <span data-ttu-id="18559-268">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="18559-269">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="18559-270">Agrega una tabla [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="18559-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="18559-271">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="18559-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="18559-272">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="18559-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="18559-273">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="18559-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="18559-274">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="18559-275">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-275">Build the app.</span></span>

<span data-ttu-id="18559-276">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="18559-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="18559-277">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="18559-277">Update the following pages:</span></span>

* <span data-ttu-id="18559-278">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="18559-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="18559-279">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="18559-280">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="18559-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="18559-281">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="18559-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="18559-282">Si la aplicación se ejecuta ahora, la aplicación produce `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="18559-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="18559-283">Este error se debe a que la clase del modelo Movie actualizado es diferente al esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="18559-284">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="18559-285">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="18559-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="18559-286">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="18559-287">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="18559-288">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="18559-289">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="18559-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="18559-290">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="18559-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="18559-291">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="18559-292">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="18559-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="18559-293">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="18559-294">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="18559-295">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="18559-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="18559-296">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="18559-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="18559-297">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="18559-298">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="18559-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="18559-299">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="18559-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18559-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18559-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="18559-301">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="18559-301">Add a migration for the rating field</span></span>

<span data-ttu-id="18559-302">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="18559-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="18559-303">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="18559-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="18559-304">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="18559-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="18559-305">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="18559-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="18559-306">Cree el código pertinente para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="18559-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="18559-307">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="18559-308">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="18559-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="18559-309">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="18559-310">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="18559-311">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="18559-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="18559-312">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="18559-313">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="18559-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="18559-314">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="18559-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="18559-315">Haga clic con el botón derecho en la base de datos y seleccione **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="18559-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="18559-316">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="18559-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="18559-317">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="18559-317">Select **OK**.</span></span>
* <span data-ttu-id="18559-318">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="18559-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="18559-319">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="18559-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="18559-320">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="18559-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="18559-321">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="18559-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="18559-322">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="18559-323">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="18559-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="18559-324">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="18559-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="18559-325">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="18559-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="18559-326">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="18559-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="18559-327">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="18559-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="18559-328">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="18559-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="18559-329">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="18559-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="18559-330">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-330">Creating a new table.</span></span>
>* <span data-ttu-id="18559-331">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="18559-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="18559-332">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="18559-332">Dropping the old table.</span></span>
>* <span data-ttu-id="18559-333">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="18559-333">Renaming the new table.</span></span>
>
><span data-ttu-id="18559-334">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="18559-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="18559-335">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="18559-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="18559-336">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="18559-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="18559-337">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="18559-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="18559-338">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="18559-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="18559-339">Elimine la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="18559-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="18559-340">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="18559-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="18559-341">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="18559-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="18559-342">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="18559-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="18559-343">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="18559-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18559-344">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="18559-344">Additional resources</span></span>

* [<span data-ttu-id="18559-345">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="18559-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="18559-346">[Anterior: Adición de búsqueda ](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="18559-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end