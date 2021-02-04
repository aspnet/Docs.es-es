---
title: 'Tutorial: Lectura de datos relacionados: ASP.NET MVC con EF Core'
description: En este tutorial podrá leer y mostrar datos relacionados, es decir, los datos que Entity Framework carga en propiedades de navegación.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 5f0e8efdf57e0a8ba5483680224d33ea5d60f06a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530156"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="6f09d-103">Tutorial: Lectura de datos relacionados: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="6f09d-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="6f09d-104">En el tutorial anterior, completó el modelo de datos School.</span><span class="sxs-lookup"><span data-stu-id="6f09d-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="6f09d-105">En este tutorial podrá leer y mostrar datos relacionados, es decir, los datos que Entity Framework carga en propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="6f09d-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="6f09d-106">En las ilustraciones siguientes se muestran las páginas con las que va a trabajar.</span><span class="sxs-lookup"><span data-stu-id="6f09d-106">The following illustrations show the pages that you'll work with.</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index.png)

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

<span data-ttu-id="6f09d-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="6f09d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f09d-110">Obtiene información sobre cómo cargar datos relacionados</span><span class="sxs-lookup"><span data-stu-id="6f09d-110">Learn how to load related data</span></span>
> * <span data-ttu-id="6f09d-111">Crea una página de cursos</span><span class="sxs-lookup"><span data-stu-id="6f09d-111">Create a Courses page</span></span>
> * <span data-ttu-id="6f09d-112">Crea una página de instructores</span><span class="sxs-lookup"><span data-stu-id="6f09d-112">Create an Instructors page</span></span>
> * <span data-ttu-id="6f09d-113">Obtiene información sobre la carga explícita</span><span class="sxs-lookup"><span data-stu-id="6f09d-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f09d-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="6f09d-114">Prerequisites</span></span>

* [<span data-ttu-id="6f09d-115">Creación de un modelo de datos complejo</span><span class="sxs-lookup"><span data-stu-id="6f09d-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="6f09d-116">Obtiene información sobre cómo cargar datos relacionados</span><span class="sxs-lookup"><span data-stu-id="6f09d-116">Learn how to load related data</span></span>

<span data-ttu-id="6f09d-117">Existen varias formas para que el software de asignación relacional de objetos (ORM) como Entity Framework pueda cargar datos relacionados en las propiedades de navegación de una entidad:</span><span class="sxs-lookup"><span data-stu-id="6f09d-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="6f09d-118">Carga diligente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-118">Eager loading.</span></span> <span data-ttu-id="6f09d-119">Cuando se lee la entidad, junto a ella se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="6f09d-120">Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan.</span><span class="sxs-lookup"><span data-stu-id="6f09d-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="6f09d-121">En Entity Framework Core, la carga diligente se especifica mediante los métodos `Include` y `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="6f09d-123">Puede recuperar algunos de los datos en distintas consultas y EF "corregirá" las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="6f09d-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="6f09d-124">Es decir, EF agrega automáticamente las entidades recuperadas por separado que pertenecen a propiedades de navegación de entidades recuperadas previamente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="6f09d-125">Para la consulta que recupera los datos relacionados, puede usar el método `Load` en lugar de un método que devuelva una lista o un objeto, como `ToList` o `Single`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="6f09d-127">Carga explícita.</span><span class="sxs-lookup"><span data-stu-id="6f09d-127">Explicit loading.</span></span> <span data-ttu-id="6f09d-128">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6f09d-129">Se escribe código que recupera los datos relacionados si son necesarios.</span><span class="sxs-lookup"><span data-stu-id="6f09d-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="6f09d-130">Como en el caso de la carga diligente con consultas independientes, la carga explícita da como resultado varias consultas que se envían a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="6f09d-131">La diferencia es que con la carga explícita el código especifica las propiedades de navegación que se van a cargar.</span><span class="sxs-lookup"><span data-stu-id="6f09d-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="6f09d-132">En Entity Framework Core 1.1 se puede usar el método `Load` para realizar la carga explícita.</span><span class="sxs-lookup"><span data-stu-id="6f09d-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="6f09d-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="6f09d-133">For example:</span></span>

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="6f09d-135">Carga diferida.</span><span class="sxs-lookup"><span data-stu-id="6f09d-135">Lazy loading.</span></span> <span data-ttu-id="6f09d-136">Cuando la entidad se lee por primera vez, no se recuperan datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6f09d-137">Pero la primera vez que intente obtener acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="6f09d-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="6f09d-138">Cada vez que intente obtener datos de una propiedad de navegación por primera vez, se envía una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="6f09d-139">Entity Framework Core 1.0 no admite la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="6f09d-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="6f09d-140">Consideraciones sobre el rendimiento</span><span class="sxs-lookup"><span data-stu-id="6f09d-140">Performance considerations</span></span>

<span data-ttu-id="6f09d-141">Si sabe que necesita datos relacionados para cada entidad que se recupere, la carga diligente suele ofrecer el mejor rendimiento, dado que una sola consulta que se envía a la base de datos normalmente es más eficaz que consultas independientes para cada entidad recuperada.</span><span class="sxs-lookup"><span data-stu-id="6f09d-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="6f09d-142">Por ejemplo, suponga que cada departamento tiene diez cursos relacionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="6f09d-143">Con la carga diligente de todos los datos relacionados se crearía una única consulta sencilla (de combinación) y un único recorrido de ida y vuelta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="6f09d-144">Una consulta independiente para los cursos de cada departamento crearía 11 recorridos de ida y vuelta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="6f09d-145">Los recorridos de ida y vuelta adicionales a la base de datos afectan especialmente de forma negativa al rendimiento cuando la latencia es alta.</span><span class="sxs-lookup"><span data-stu-id="6f09d-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="6f09d-146">Por otro lado, en algunos escenarios, las consultas independientes son más eficaces.</span><span class="sxs-lookup"><span data-stu-id="6f09d-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="6f09d-147">Es posible que la carga diligente de todos los datos relacionados en una consulta genere una combinación muy compleja que SQL Server no pueda procesar eficazmente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="6f09d-148">O bien, si necesita tener acceso a las propiedades de navegación de una entidad solo para un subconjunto de un conjunto de las entidades que está procesando, es posible que las consultas independientes den mejores resultados porque la carga diligente de todo el contenido por adelantado recuperaría más datos de los que necesita.</span><span class="sxs-lookup"><span data-stu-id="6f09d-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="6f09d-149">Si el rendimiento es crítico, es mejor probarlo de ambas formas para elegir la mejor opción.</span><span class="sxs-lookup"><span data-stu-id="6f09d-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="6f09d-150">Crea una página de cursos</span><span class="sxs-lookup"><span data-stu-id="6f09d-150">Create a Courses page</span></span>

<span data-ttu-id="6f09d-151">La entidad Course incluye una propiedad de navegación que contiene la entidad Department del departamento al que se asigna el curso.</span><span class="sxs-lookup"><span data-stu-id="6f09d-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="6f09d-152">Para mostrar el nombre del departamento asignado en una lista de cursos, tendrá que obtener la propiedad Name de la entidad Department que se encuentra en la propiedad de navegación `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="6f09d-153">Cree un controlador denominado CoursesController para el tipo de entidad Course, con las mismas opciones para el proveedor de scaffolding **Controlador de MVC con vistas que usan Entity Framework** que usó anteriormente para el controlador de Students, como se muestra en la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="6f09d-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Agregar el controlador de cursos](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="6f09d-155">Abra *CoursesController.cs* y examine el método `Index`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="6f09d-156">El scaffolding automático ha especificado la carga diligente para la propiedad de navegación `Department` mediante el método `Include`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="6f09d-157">Reemplace el método `Index` con el siguiente código, en el que se usa un nombre más adecuado para la `IQueryable` que devuelve las entidades Course (`courses` en lugar de `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="6f09d-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="6f09d-158">Abra *Views/Courses/Index.cshtml* y reemplace el código de plantilla con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="6f09d-159">Se resaltan los cambios:</span><span class="sxs-lookup"><span data-stu-id="6f09d-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="6f09d-160">Ha realizado los cambios siguientes en el código con scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6f09d-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="6f09d-161">Ha cambiado el título de Index a Courses.</span><span class="sxs-lookup"><span data-stu-id="6f09d-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="6f09d-162">Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="6f09d-163">De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales.</span><span class="sxs-lookup"><span data-stu-id="6f09d-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="6f09d-164">Pero en este caso, la clave principal es significativa y quiere mostrarla.</span><span class="sxs-lookup"><span data-stu-id="6f09d-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="6f09d-165">Ha cambiado la columna **Department** para mostrar el nombre del departamento.</span><span class="sxs-lookup"><span data-stu-id="6f09d-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="6f09d-166">El código muestra la propiedad `Name` de la entidad Department que se carga en la propiedad de navegación `Department`:</span><span class="sxs-lookup"><span data-stu-id="6f09d-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="6f09d-167">Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="6f09d-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página de índice de cursos](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="6f09d-169">Crea una página de instructores</span><span class="sxs-lookup"><span data-stu-id="6f09d-169">Create an Instructors page</span></span>

<span data-ttu-id="6f09d-170">En esta sección, creará un controlador y una vista de la entidad Instructor con el fin de mostrar la página Instructors:</span><span class="sxs-lookup"><span data-stu-id="6f09d-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

<span data-ttu-id="6f09d-172">En esta página se leen y muestran los datos relacionados de las maneras siguientes:</span><span class="sxs-lookup"><span data-stu-id="6f09d-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="6f09d-173">En la lista de instructores se muestran datos relacionados de la entidad OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="6f09d-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="6f09d-174">Las entidades Instructor y OfficeAssignment se encuentran en una relación de uno a cero o uno.</span><span class="sxs-lookup"><span data-stu-id="6f09d-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="6f09d-175">Usará la carga diligente para las entidades OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="6f09d-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="6f09d-176">Como se explicó anteriormente, la carga diligente normalmente es más eficaz cuando se necesitan los datos relacionados para todas las filas recuperadas de la tabla principal.</span><span class="sxs-lookup"><span data-stu-id="6f09d-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="6f09d-177">En este caso, quiere mostrar las asignaciones de oficina para todos los instructores que se muestran.</span><span class="sxs-lookup"><span data-stu-id="6f09d-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="6f09d-178">Cuando el usuario selecciona un instructor, se muestran las entidades Course relacionadas.</span><span class="sxs-lookup"><span data-stu-id="6f09d-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="6f09d-179">Las entidades Instructor y Course se encuentran en una relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="6f09d-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="6f09d-180">Usará la carga diligente para las entidades Course y sus entidades Department relacionadas.</span><span class="sxs-lookup"><span data-stu-id="6f09d-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="6f09d-181">En este caso, es posible que las consultas independientes sean más eficaces porque necesita cursos solo para el instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="6f09d-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="6f09d-182">Pero en este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación dentro de entidades que, a su vez, se encuentran en propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="6f09d-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="6f09d-183">Cuando el usuario selecciona un curso, se muestran los datos relacionados del conjunto de entidades Enrollments.</span><span class="sxs-lookup"><span data-stu-id="6f09d-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="6f09d-184">Las entidades Course y Enrollment están en una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="6f09d-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="6f09d-185">Usará consultas independientes para las entidades Enrollment y sus entidades Student relacionadas.</span><span class="sxs-lookup"><span data-stu-id="6f09d-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="6f09d-186">Crear un modelo de vista para la vista de índice de instructores</span><span class="sxs-lookup"><span data-stu-id="6f09d-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="6f09d-187">En la página Instructors se muestran datos de tres tablas diferentes.</span><span class="sxs-lookup"><span data-stu-id="6f09d-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="6f09d-188">Por tanto, creará un modelo de vista que incluye tres propiedades, cada una con los datos de una de las tablas.</span><span class="sxs-lookup"><span data-stu-id="6f09d-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="6f09d-189">En la carpeta *SchoolViewModels*, cree *InstructorIndexData.cs* y reemplace el código existente con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6f09d-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="6f09d-190">Crear el controlador y las vistas de Instructor</span><span class="sxs-lookup"><span data-stu-id="6f09d-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="6f09d-191">Cree un controlador Instructors con acciones de lectura y escritura de EF como se muestra en la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="6f09d-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Adición del controlador de instructores](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="6f09d-193">Abra *InstructorsController.cs* y agregue una instrucción using para el espacio de nombres ViewModels:</span><span class="sxs-lookup"><span data-stu-id="6f09d-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="6f09d-194">Reemplace el método Index con el código siguiente para realizar la carga diligente de los datos relacionados y colocarlos en el modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="6f09d-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="6f09d-195">El método acepta datos de ruta opcionales (`id`) y un parámetro de cadena de consulta (`courseID`) que proporcionan los valores ID del instructor y el curso seleccionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="6f09d-196">Los parámetros se proporcionan mediante los hipervínculos **Select** de la página.</span><span class="sxs-lookup"><span data-stu-id="6f09d-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="6f09d-197">El código comienza creando una instancia del modelo de vista y coloca en ella la lista de instructores.</span><span class="sxs-lookup"><span data-stu-id="6f09d-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="6f09d-198">El código especifica la carga diligente para `Instructor.OfficeAssignment` y las propiedades de navegación de `Instructor.CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="6f09d-199">Dentro de la propiedad `CourseAssignments` se carga la propiedad `Course` y dentro de esta se cargan las propiedades `Enrollments` y `Department`, y dentro de cada entidad `Enrollment` se carga la propiedad `Student`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="6f09d-200">Como la vista siempre requiere la entidad OfficeAssignment, resulta más eficaz capturarla en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="6f09d-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="6f09d-201">Las entidades Course son necesarias cuando se selecciona un instructor en la página web, por lo que una sola consulta es más adecuada que varias solo si la página se muestra con más frecuencia con un curso seleccionado que sin él.</span><span class="sxs-lookup"><span data-stu-id="6f09d-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="6f09d-202">El código repite `CourseAssignments` y `Course` porque se necesitan dos propiedades de `Course`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="6f09d-203">En la primera cadena de llamadas `ThenInclude` se obtiene `CourseAssignment.Course`, `Course.Enrollments` y `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="6f09d-204">En ese punto del código, otro elemento `ThenInclude` sería para las propiedades de navegación de `Student`, lo que no es necesario.</span><span class="sxs-lookup"><span data-stu-id="6f09d-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="6f09d-205">Pero la llamada a `Include` se inicia con las propiedades de `Instructor`, por lo que tendrá que volver a pasar por la cadena, especificando esta vez `Course.Department` en lugar de `Course.Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="6f09d-206">El código siguiente se ejecuta cuando se ha seleccionado un instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="6f09d-207">El instructor seleccionado se recupera de la lista de instructores del modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="6f09d-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="6f09d-208">Después, se carga la propiedad `Courses` del modelo de vista con las entidades Course de la propiedad de navegación `CourseAssignments` de ese instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="6f09d-209">El método `Where` devuelve una colección, pero en este caso los criterios que se pasan a ese método dan como resultado que solo se devuelva una entidad Instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="6f09d-210">El método `Single` convierte la colección en una única entidad Instructor, que proporciona acceso a la propiedad `CourseAssignments` de esa entidad.</span><span class="sxs-lookup"><span data-stu-id="6f09d-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="6f09d-211">La propiedad `CourseAssignments` contiene entidades `CourseAssignment`, de las que solo quiere las entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="6f09d-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="6f09d-212">El método `Single` se usa en una colección cuando se sabe que la colección tendrá un único elemento.</span><span class="sxs-lookup"><span data-stu-id="6f09d-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="6f09d-213">El método Single inicia una excepción si la colección que se pasa está vacía o si hay más de un elemento.</span><span class="sxs-lookup"><span data-stu-id="6f09d-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="6f09d-214">Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía.</span><span class="sxs-lookup"><span data-stu-id="6f09d-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="6f09d-215">Pero en este caso, eso seguiría iniciando una excepción (al tratar de buscar una propiedad `Courses` en una referencia nula), y el mensaje de excepción indicaría con menos claridad la causa del problema.</span><span class="sxs-lookup"><span data-stu-id="6f09d-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="6f09d-216">Cuando se llama al método `Single`, también se puede pasar la condición Where en lugar de llamar al método `Where` por separado:</span><span class="sxs-lookup"><span data-stu-id="6f09d-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="6f09d-217">En lugar de:</span><span class="sxs-lookup"><span data-stu-id="6f09d-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="6f09d-218">A continuación, si se ha seleccionado un curso, se recupera de la lista de cursos en el modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="6f09d-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="6f09d-219">Después, se carga la propiedad `Enrollments` del modelo de vista con las entidades Enrollment de la propiedad de navegación `Enrollments` de ese curso.</span><span class="sxs-lookup"><span data-stu-id="6f09d-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="6f09d-220">Modificar la vista de índice de instructores</span><span class="sxs-lookup"><span data-stu-id="6f09d-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="6f09d-221">En *Views/Instructors/Index.cshtml*, reemplace el código de plantilla con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="6f09d-222">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-222">The changes are highlighted.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

<span data-ttu-id="6f09d-223">Ha realizado los cambios siguientes en el código existente:</span><span class="sxs-lookup"><span data-stu-id="6f09d-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="6f09d-224">Ha cambiado la clase de modelo por `InstructorIndexData`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="6f09d-225">Ha cambiado el título de la página de **Index** a **Instructors**.</span><span class="sxs-lookup"><span data-stu-id="6f09d-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="6f09d-226">Se ha agregado una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL.</span><span class="sxs-lookup"><span data-stu-id="6f09d-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="6f09d-227">(Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada).</span><span class="sxs-lookup"><span data-stu-id="6f09d-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="6f09d-228">Se ha agregado una columna **Courses** en la que se muestran los cursos que imparte cada instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="6f09d-229">Para obtener más información, vea la sección [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition) del artículo sobre la sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="6f09d-230">Se ha agregado código que agrega condicionalmente una clase CSS de Bootstrap al elemento `tr` del instructor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="6f09d-230">Added code that conditionally adds a Bootstrap CSS class to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="6f09d-231">Esta clase establece el color de fondo de la fila seleccionada.</span><span class="sxs-lookup"><span data-stu-id="6f09d-231">This class sets a background color for the selected row.</span></span>

* <span data-ttu-id="6f09d-232">Se ha agregado un hipervínculo nuevo con la etiqueta **Select** inmediatamente antes de los otros vínculos de cada fila, lo que hace que el identificador del instructor seleccionado se envíe al método `Index`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="6f09d-233">Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la propiedad Location de las entidades OfficeAssignment relacionadas y una celda de tabla vacía cuando no hay ninguna entidad OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="6f09d-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Página de índice de instructores sin ninguna selección](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="6f09d-235">En el archivo *Views/Instructors/Index.cshtml*, después del elemento de tabla de cierre (situado al final del archivo), agregue el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="6f09d-236">Este código muestra una lista de cursos relacionados con un instructor cuando se selecciona un instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

<span data-ttu-id="6f09d-237">Este código lee la propiedad `Courses` del modelo de vista para mostrar una lista de cursos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="6f09d-238">También proporciona un hipervínculo **Select** que envía el identificador del curso seleccionado al método de acción `Index`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="6f09d-239">Actualice la página y seleccione un instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="6f09d-240">Ahora verá una cuadrícula en la que se muestran los cursos asignados al instructor seleccionado, y para cada curso, el nombre del departamento asignado.</span><span class="sxs-lookup"><span data-stu-id="6f09d-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Instructor seleccionado en la página de índice de instructores](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="6f09d-242">Después del bloque de código que se acaba de agregar, agregue el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f09d-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="6f09d-243">Esto muestra una lista de los estudiantes que están inscritos en un curso cuando se selecciona ese curso.</span><span class="sxs-lookup"><span data-stu-id="6f09d-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="6f09d-244">Este código lee la propiedad Enrollments del modelo de vista para mostrar una lista de los estudiantes inscritos en el curso.</span><span class="sxs-lookup"><span data-stu-id="6f09d-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="6f09d-245">Vuelva a actualizar la página y seleccione un instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="6f09d-246">Después, seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.</span><span class="sxs-lookup"><span data-stu-id="6f09d-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="6f09d-248">Acerca de la carga explícita</span><span class="sxs-lookup"><span data-stu-id="6f09d-248">About explicit loading</span></span>

<span data-ttu-id="6f09d-249">Cuando se recuperó la lista de instructores en *InstructorsController.cs*, se especificó la carga diligente de la propiedad de navegación `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="6f09d-250">Suponga que esperaba que los usuarios rara vez quisieran ver las inscripciones en un instructor y curso seleccionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="6f09d-251">En ese caso, es posible que quiera cargar los datos de inscripción solo si se solicitan.</span><span class="sxs-lookup"><span data-stu-id="6f09d-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="6f09d-252">Para ver un ejemplo de cómo realizar la carga explícita, reemplace el método `Index` con el código siguiente, que quita la carga diligente de Enrollments y carga explícitamente esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="6f09d-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="6f09d-253">Los cambios de código aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="6f09d-254">El nuevo código quita las llamadas al método *ThenInclude* para los datos de inscripción del código que recupera las entidades Instructor.</span><span class="sxs-lookup"><span data-stu-id="6f09d-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="6f09d-255">También quita `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="6f09d-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="6f09d-256">Si se seleccionan un instructor y un curso, el código resaltado recupera las entidades Enrollment para el curso seleccionado y las entidades Student de cada inscripción.</span><span class="sxs-lookup"><span data-stu-id="6f09d-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="6f09d-257">Ejecute la aplicación, vaya a la página de índice de instructores ahora y no verá ninguna diferencia en lo que se muestra en la página, aunque haya cambiado la forma en que se recuperan los datos.</span><span class="sxs-lookup"><span data-stu-id="6f09d-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6f09d-258">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="6f09d-258">Get the code</span></span>

[<span data-ttu-id="6f09d-259">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="6f09d-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="6f09d-260">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="6f09d-260">Next steps</span></span>

<span data-ttu-id="6f09d-261">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="6f09d-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f09d-262">Obtenido información sobre cómo cargar datos relacionados</span><span class="sxs-lookup"><span data-stu-id="6f09d-262">Learned how to load related data</span></span>
> * <span data-ttu-id="6f09d-263">Creado una página de cursos</span><span class="sxs-lookup"><span data-stu-id="6f09d-263">Created a Courses page</span></span>
> * <span data-ttu-id="6f09d-264">Creado una página de instructores</span><span class="sxs-lookup"><span data-stu-id="6f09d-264">Created an Instructors page</span></span>
> * <span data-ttu-id="6f09d-265">Obtenido información sobre la carga explícita</span><span class="sxs-lookup"><span data-stu-id="6f09d-265">Learned about explicit loading</span></span>

<span data-ttu-id="6f09d-266">Pase al tutorial siguiente para obtener información sobre cómo actualizar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="6f09d-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6f09d-267">Actualización de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="6f09d-267">Update related data</span></span>](update-related-data.md)
