---
title: Parte 7, adición de un campo nuevo
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360884"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="88712-103">Parte 7. Adición de un campo nuevo a una instancia de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88712-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="88712-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="88712-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="88712-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88712-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="88712-106">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="88712-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="88712-107">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-107">Add a new field to the model.</span></span>
* <span data-ttu-id="88712-108">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="88712-109">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="88712-110">Agrega una tabla [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="88712-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="88712-111">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="88712-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="88712-112">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="88712-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="88712-113">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="88712-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="88712-114">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="88712-115">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-115">Build the app.</span></span>

1. <span data-ttu-id="88712-116">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="88712-117">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="88712-117">Update the following pages:</span></span>
   1. <span data-ttu-id="88712-118">Agregue el campo `Rating` a las páginas Delete (Eliminar) y Details (Detalles).</span><span class="sxs-lookup"><span data-stu-id="88712-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="88712-119">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="88712-120">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="88712-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="88712-121">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="88712-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="88712-122">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="88712-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="88712-123">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="88712-124">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="88712-125">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="88712-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="88712-126">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="88712-127">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="88712-128">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="88712-129">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="88712-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="88712-130">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="88712-131">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="88712-132">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="88712-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="88712-133">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="88712-134">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="88712-135">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="88712-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="88712-136">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="88712-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="88712-137">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="88712-138">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="88712-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="88712-139">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="88712-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88712-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88712-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="88712-141">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="88712-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="88712-142">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="88712-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="88712-143">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="88712-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="88712-144">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="88712-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="88712-145">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="88712-146">Create código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="88712-147">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="88712-148">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="88712-149">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="88712-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="88712-150">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="88712-151">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="88712-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="88712-152">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="88712-153">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="88712-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="88712-154">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="88712-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="88712-155">Haga clic con el botón derecho en la base de datos y seleccione **Delete** .</span><span class="sxs-lookup"><span data-stu-id="88712-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="88712-156">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="88712-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="88712-157">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="88712-157">Select **OK**.</span></span>
1. <span data-ttu-id="88712-158">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="88712-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="88712-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="88712-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="88712-160">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="88712-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="88712-161">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="88712-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="88712-162">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="88712-163">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="88712-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="88712-164">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="88712-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="88712-165">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="88712-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="88712-166">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="88712-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="88712-167">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="88712-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="88712-168">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="88712-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="88712-169">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="88712-170">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-170">Creating a new table.</span></span>
>* <span data-ttu-id="88712-171">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="88712-172">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="88712-172">Dropping the old table.</span></span>
>* <span data-ttu-id="88712-173">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="88712-173">Renaming the new table.</span></span>
>
><span data-ttu-id="88712-174">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="88712-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="88712-175">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="88712-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="88712-176">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="88712-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="88712-177">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="88712-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="88712-178">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="88712-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="88712-179">Delete la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="88712-180">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="88712-181">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="88712-182">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="88712-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88712-183">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="88712-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="88712-184">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="88712-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="88712-185">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88712-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="88712-186">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="88712-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="88712-187">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-187">Add a new field to the model.</span></span>
* <span data-ttu-id="88712-188">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="88712-189">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="88712-190">Agrega una tabla [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="88712-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="88712-191">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="88712-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="88712-192">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="88712-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="88712-193">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="88712-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="88712-194">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="88712-195">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-195">Build the app.</span></span>

1. <span data-ttu-id="88712-196">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="88712-197">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="88712-197">Update the following pages:</span></span>
   1. <span data-ttu-id="88712-198">Agregue el campo `Rating` a las páginas Delete (Eliminar) y Details (Detalles).</span><span class="sxs-lookup"><span data-stu-id="88712-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="88712-199">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="88712-200">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="88712-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="88712-201">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="88712-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="88712-202">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="88712-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="88712-203">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="88712-204">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="88712-205">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="88712-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="88712-206">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="88712-207">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="88712-208">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="88712-209">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="88712-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="88712-210">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="88712-211">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="88712-212">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="88712-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="88712-213">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="88712-214">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="88712-215">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="88712-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="88712-216">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="88712-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="88712-217">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="88712-218">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="88712-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="88712-219">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="88712-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88712-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88712-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="88712-221">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="88712-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="88712-222">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="88712-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="88712-223">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="88712-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="88712-224">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="88712-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="88712-225">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="88712-226">Create código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="88712-227">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="88712-228">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="88712-229">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="88712-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="88712-230">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="88712-231">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="88712-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="88712-232">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="88712-233">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="88712-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="88712-234">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="88712-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="88712-235">Haga clic con el botón derecho en la base de datos y seleccione **Delete** .</span><span class="sxs-lookup"><span data-stu-id="88712-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="88712-236">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="88712-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="88712-237">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="88712-237">Select **OK**.</span></span>
* <span data-ttu-id="88712-238">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="88712-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="88712-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="88712-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="88712-240">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="88712-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="88712-241">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="88712-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="88712-242">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="88712-243">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="88712-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="88712-244">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="88712-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="88712-245">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="88712-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="88712-246">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="88712-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="88712-247">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="88712-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="88712-248">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="88712-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="88712-249">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="88712-250">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-250">Creating a new table.</span></span>
>* <span data-ttu-id="88712-251">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="88712-252">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="88712-252">Dropping the old table.</span></span>
>* <span data-ttu-id="88712-253">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="88712-253">Renaming the new table.</span></span>
>
><span data-ttu-id="88712-254">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="88712-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="88712-255">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="88712-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="88712-256">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="88712-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="88712-257">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="88712-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="88712-258">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="88712-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="88712-259">Delete la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="88712-260">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="88712-261">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="88712-262">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="88712-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88712-263">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="88712-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="88712-264">[Anterior: ](xref:tutorials/razor-pages/search)Adición de búsqueda 
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="88712-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88712-265">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88712-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="88712-266">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="88712-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="88712-267">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-267">Add a new field to the model.</span></span>
* <span data-ttu-id="88712-268">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="88712-269">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="88712-270">Agrega una tabla [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="88712-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="88712-271">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="88712-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="88712-272">La comprobación automática de la sincronización del esquema y el modelo facilita la detección de problemas de código de base de datos incoherente.</span><span class="sxs-lookup"><span data-stu-id="88712-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="88712-273">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="88712-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="88712-274">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="88712-275">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-275">Build the app.</span></span>

<span data-ttu-id="88712-276">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="88712-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="88712-277">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="88712-277">Update the following pages:</span></span>

* <span data-ttu-id="88712-278">Agregue el campo `Rating` a las páginas Delete (Eliminar) y Details (Detalles).</span><span class="sxs-lookup"><span data-stu-id="88712-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="88712-279">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="88712-280">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="88712-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="88712-281">La aplicación no funcionará hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="88712-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="88712-282">Si la aplicación se ejecuta ahora, la aplicación produce `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="88712-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="88712-283">Este error se debe a que la clase del modelo Movie actualizado es diferente al esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="88712-284">No hay ninguna columna `Rating` en la tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="88712-285">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="88712-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="88712-286">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="88712-287">Este enfoque resulta conveniente al principio del ciclo de desarrollo, permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="88712-288">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="88712-289">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="88712-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="88712-290">El hecho de quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="88712-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="88712-291">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="88712-292">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="88712-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="88712-293">Haga este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="88712-294">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="88712-295">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="88712-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="88712-296">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="88712-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="88712-297">A continuación se muestra un cambio de ejemplo, pero realice este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="88712-298">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="88712-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="88712-299">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="88712-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="88712-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88712-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="88712-301">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="88712-301">Add a migration for the rating field</span></span>

<span data-ttu-id="88712-302">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="88712-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="88712-303">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="88712-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="88712-304">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="88712-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="88712-305">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="88712-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="88712-306">Create código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="88712-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="88712-307">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="88712-308">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="88712-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="88712-309">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="88712-310">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-310">If you delete all the records in the DdatabaseB, the initializer will seed the DdatabaseB and include the `Rating` field.</span></span> <span data-ttu-id="88712-311">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="88712-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="88712-312">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="88712-313">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="88712-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="88712-314">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="88712-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="88712-315">Haga clic con el botón derecho en la base de datos y seleccione **Delete** .</span><span class="sxs-lookup"><span data-stu-id="88712-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="88712-316">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="88712-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="88712-317">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="88712-317">Select **OK**.</span></span>
* <span data-ttu-id="88712-318">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="88712-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="88712-319">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="88712-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="88712-320">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="88712-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="88712-321">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="88712-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="88712-322">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="88712-323">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="88712-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="88712-324">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="88712-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="88712-325">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="88712-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="88712-326">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="88712-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="88712-327">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="88712-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="88712-328">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="88712-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="88712-329">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="88712-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="88712-330">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-330">Creating a new table.</span></span>
>* <span data-ttu-id="88712-331">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="88712-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="88712-332">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="88712-332">Dropping the old table.</span></span>
>* <span data-ttu-id="88712-333">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="88712-333">Renaming the new table.</span></span>
>
><span data-ttu-id="88712-334">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="88712-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="88712-335">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="88712-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="88712-336">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="88712-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="88712-337">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="88712-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="88712-338">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="88712-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="88712-339">Delete la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88712-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="88712-340">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="88712-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="88712-341">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="88712-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="88712-342">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="88712-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="88712-343">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="88712-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88712-344">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="88712-344">Additional resources</span></span>

* [<span data-ttu-id="88712-345">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="88712-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="88712-346">[Anterior: Adición de búsqueda ](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="88712-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
