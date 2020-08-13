---
title: Parte 7. Adición de un campo nuevo a una instancia de Razor Pages en ASP.NET Core
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 07a28333f86bb9b3c9f07b3ddf964edf5bf8dc96
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022048"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="c2398-103">Parte 7. Adición de un campo nuevo a una instancia de Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2398-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="c2398-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c2398-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c2398-105">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="c2398-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c2398-106">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-106">Add a new field to the model.</span></span>
* <span data-ttu-id="c2398-107">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c2398-108">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2398-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c2398-109">Agrega una tabla `__EFMigrationsHistory` a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="c2398-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c2398-110">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="c2398-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="c2398-111">La comprobación automática de la sincronización del esquema/modelo facilita la detección de problemas de código o base de datos incoherentes.</span><span class="sxs-lookup"><span data-stu-id="c2398-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c2398-112">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="c2398-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c2398-113">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="c2398-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c2398-114">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2398-114">Build the app.</span></span>

<span data-ttu-id="c2398-115">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="c2398-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="c2398-116">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2398-116">Update the following pages:</span></span>

* <span data-ttu-id="c2398-117">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="c2398-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="c2398-118">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="c2398-119">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="c2398-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c2398-120">La aplicación no funciona hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="c2398-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="c2398-121">Al ejecutar la aplicación sin actualizar la base de datos, se produce una excepción `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="c2398-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c2398-122">La excepción `SqlException` se debe a que la clase del modelo Movie actualizado es diferente del esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c2398-123">(No hay ninguna columna `Rating` en la tabla de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="c2398-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="c2398-124">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="c2398-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c2398-125">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c2398-126">Este enfoque resulta conveniente al principio del ciclo de desarrollo; permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c2398-127">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c2398-128">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="c2398-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="c2398-129">El quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2398-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c2398-130">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c2398-131">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="c2398-132">Puede realizar este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c2398-133">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c2398-134">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="c2398-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c2398-135">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="c2398-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c2398-136">A continuación se muestra un cambio de ejemplo, aunque es conveniente realizar este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="c2398-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c2398-137">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="c2398-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c2398-138">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="c2398-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2398-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2398-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c2398-140">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="c2398-140">Add a migration for the rating field</span></span>

<span data-ttu-id="c2398-141">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="c2398-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c2398-142">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="c2398-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c2398-143">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="c2398-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c2398-144">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c2398-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="c2398-145">Cree código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="c2398-146">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="c2398-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c2398-147">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="c2398-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c2398-148">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos y que conserve los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="c2398-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c2398-149">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="c2398-150">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c2398-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c2398-151">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c2398-152">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="c2398-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c2398-153">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="c2398-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="c2398-154">Haga clic con el botón derecho en la base de datos y seleccione *Eliminar*.</span><span class="sxs-lookup"><span data-stu-id="c2398-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="c2398-155">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="c2398-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c2398-156">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="c2398-156">Select **OK**.</span></span>
* <span data-ttu-id="c2398-157">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="c2398-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2398-158">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2398-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c2398-159">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="c2398-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="c2398-160">Elimine la carpeta de migración.</span><span class="sxs-lookup"><span data-stu-id="c2398-160">Delete the migration folder.</span></span>  <span data-ttu-id="c2398-161">Use estos comandos para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="c2398-162">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c2398-163">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c2398-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2398-164">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c2398-164">Additional resources</span></span>

* [<span data-ttu-id="c2398-165">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="c2398-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="c2398-166">[Anterior: Adición de búsqueda](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c2398-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c2398-167">En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:</span><span class="sxs-lookup"><span data-stu-id="c2398-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c2398-168">Agregar un campo nuevo al modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-168">Add a new field to the model.</span></span>
* <span data-ttu-id="c2398-169">Migrar el cambio de esquema del campo nuevo a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c2398-170">Al usar EF Code First para crear una base de datos automáticamente, Code First hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2398-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c2398-171">Agrega una tabla a la base de datos para ayudar a saber si el esquema de la base de datos está sincronizado con las clases del modelo a partir del que se ha generado.</span><span class="sxs-lookup"><span data-stu-id="c2398-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c2398-172">Si las clases del modelo no están sincronizadas con la base de datos, EF produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="c2398-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="c2398-173">La comprobación automática de la sincronización del esquema/modelo facilita la detección de problemas de código o base de datos incoherentes.</span><span class="sxs-lookup"><span data-stu-id="c2398-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c2398-174">Adición de una propiedad de clasificación al modelo Movie</span><span class="sxs-lookup"><span data-stu-id="c2398-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c2398-175">Abra el archivo *Models/Movie.cs* y agregue una propiedad `Rating`:</span><span class="sxs-lookup"><span data-stu-id="c2398-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c2398-176">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2398-176">Build the app.</span></span>

<span data-ttu-id="c2398-177">Edite *Pages/Movies/Index.cshtml* y agregue un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="c2398-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="c2398-178">Actualice las páginas siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2398-178">Update the following pages:</span></span>

* <span data-ttu-id="c2398-179">Agregue el campo `Rating` a las páginas Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="c2398-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="c2398-180">Actualice [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="c2398-181">Agregue el campo `Rating` a la página de edición.</span><span class="sxs-lookup"><span data-stu-id="c2398-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c2398-182">La aplicación no funciona hasta que la base de datos se actualiza para incluir el nuevo campo.</span><span class="sxs-lookup"><span data-stu-id="c2398-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="c2398-183">Si se ejecuta ahora, la aplicación produce una `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="c2398-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c2398-184">Este error se debe a que la clase del modelo Movie actualizado es diferente al esquema de la tabla Movie de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c2398-185">(No hay ninguna columna `Rating` en la tabla de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="c2398-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="c2398-186">Este error se puede resolver de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="c2398-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c2398-187">Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear con el nuevo esquema de la clase del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c2398-188">Este enfoque resulta conveniente al principio del ciclo de desarrollo; permite desarrollar a la vez y de manera rápida el esquema del modelo y la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c2398-189">El inconveniente es que se pierden los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c2398-190">No use este enfoque en una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="c2398-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="c2398-191">El quitar la base de datos en los cambios de esquema y el usar un inicializador para inicializar automáticamente la base de datos con datos de prueba suele ser una forma productiva de desarrollar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2398-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c2398-192">Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c2398-193">La ventaja de este enfoque es que se conservan los datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="c2398-194">Puede realizar este cambio de forma manual o mediante la creación de un script de cambio de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c2398-195">Use Migraciones de Code First para actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c2398-196">Para este tutorial, use Migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="c2398-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c2398-197">Actualice la clase `SeedData` para que proporcione un valor para la nueva columna.</span><span class="sxs-lookup"><span data-stu-id="c2398-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c2398-198">A continuación se muestra un cambio de ejemplo, aunque es conveniente realizar este cambio para cada bloque `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="c2398-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c2398-199">Vea el [archivo completado SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="c2398-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c2398-200">Compile la solución.</span><span class="sxs-lookup"><span data-stu-id="c2398-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2398-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2398-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c2398-202">Agregar una migración para el campo de clasificación</span><span class="sxs-lookup"><span data-stu-id="c2398-202">Add a migration for the rating field</span></span>

<span data-ttu-id="c2398-203">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="c2398-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c2398-204">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="c2398-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c2398-205">El comando `Add-Migration` indica al marco de trabajo que:</span><span class="sxs-lookup"><span data-stu-id="c2398-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c2398-206">Compare el modelo `Movie` con el esquema de base de datos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c2398-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="c2398-207">Cree código para migrar el esquema de la base de datos al nuevo modelo.</span><span class="sxs-lookup"><span data-stu-id="c2398-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="c2398-208">El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="c2398-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c2398-209">Resulta útil emplear un nombre descriptivo para el archivo de migración.</span><span class="sxs-lookup"><span data-stu-id="c2398-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c2398-210">El comando `Update-Database` le indica al marco que aplique los cambios de esquema a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c2398-211">Si elimina todos los registros de la base de datos, el inicializador inicializará la base de datos e incluirá el campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="c2398-212">Puede hacerlo con los vínculos de eliminación en el explorador o desde el [Explorador de objetos de SQL Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c2398-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c2398-213">Otra opción es eliminar la base de datos y usar las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c2398-214">Para eliminar la base de datos de SSOX:</span><span class="sxs-lookup"><span data-stu-id="c2398-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c2398-215">Seleccione la base de datos en SSOX.</span><span class="sxs-lookup"><span data-stu-id="c2398-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="c2398-216">Haga clic con el botón derecho en la base de datos y seleccione *Eliminar*.</span><span class="sxs-lookup"><span data-stu-id="c2398-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="c2398-217">Active **Cerrar las conexiones existentes**.</span><span class="sxs-lookup"><span data-stu-id="c2398-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c2398-218">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="c2398-218">Select **OK**.</span></span>
* <span data-ttu-id="c2398-219">En la [PMC](xref:tutorials/razor-pages/new-field#pmc), actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="c2398-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c2398-220">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c2398-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c2398-221">Quitar y volver a crear la base de datos</span><span class="sxs-lookup"><span data-stu-id="c2398-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="c2398-222">Elimine la base de datos y use las migraciones para volver a crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2398-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c2398-223">Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="c2398-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="c2398-224">Luego, ejecute el comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="c2398-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="c2398-225">Ejecute la aplicación y compruebe que puede crear, editar o mostrar películas con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="c2398-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c2398-226">Si la base de datos no se ha propagado, establezca un punto de interrupción en el método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c2398-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2398-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c2398-227">Additional resources</span></span>

* [<span data-ttu-id="c2398-228">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="c2398-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="c2398-229">[Anterior: Adición de búsqueda](xref:tutorials/razor-pages/search)
> [Siguiente: Adición de validación](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c2398-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
