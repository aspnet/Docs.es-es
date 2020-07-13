---
title: 'Parte 7. Razor Pages con EF Core en ASP.NET Core: Actualización de datos relacionados'
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b442a4ce1f63c047c123315626f559155fd06424
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060142"
---
# <a name="part-7-razor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="ac144-103">Parte 7. Razor Pages con EF Core en ASP.NET Core: Actualización de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="ac144-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="ac144-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac144-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac144-105">En este tutorial se muestra cómo actualizar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="ac144-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="ac144-106">En las ilustraciones siguientes se muestran algunas de las páginas completadas.</span><span class="sxs-lookup"><span data-stu-id="ac144-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="ac144-107">![Página de edición de cursos](update-related-data/_static/course-edit30.png)
![Página de edición de instructores](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="ac144-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="ac144-108">Actualización de las páginas de creación y edición de cursos</span><span class="sxs-lookup"><span data-stu-id="ac144-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="ac144-109">El código con scaffolding para las páginas Course Create y Edit tiene una lista desplegable Department en la que se muestra el identificador del departamento (un entero).</span><span class="sxs-lookup"><span data-stu-id="ac144-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="ac144-110">En la lista desplegable se debe mostrar el nombre del departamento, de modo que en las dos páginas se necesita una lista de nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="ac144-111">Para proporcionar esa lista, use una clase base para las páginas Create y Edit.</span><span class="sxs-lookup"><span data-stu-id="ac144-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="ac144-112">Creación de una clase base para las páginas de creación y edición de cursos</span><span class="sxs-lookup"><span data-stu-id="ac144-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="ac144-113">Cree un archivo *Pages/Courses/DepartmentNamePageModel.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="ac144-114">En el código anterior se crea una clase [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para que contenga la lista de nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="ac144-115">Si se especifica `selectedDepartment`, se selecciona ese departamento en la `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="ac144-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="ac144-116">Las clases de modelo de página de Create y Edit se derivan de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ac144-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="ac144-117">Actualización del modelo de la página Course Create</span><span class="sxs-lookup"><span data-stu-id="ac144-117">Update the Course Create page model</span></span>

<span data-ttu-id="ac144-118">Una entidad Course (Curso) se asigna a una entidad Department (Departamento).</span><span class="sxs-lookup"><span data-stu-id="ac144-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="ac144-119">La clase base para las páginas Create y Edit proporciona un elemento `SelectList` para seleccionar el departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="ac144-120">La lista desplegable que usa el elemento `SelectList` establece la propiedad de clave externa (FK) `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="ac144-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="ac144-121">EF Core usa la FK `Course.DepartmentID` para cargar la propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="ac144-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Crear el curso](update-related-data/_static/ddl30.png)

<span data-ttu-id="ac144-123">Actualice *Pages/Courses/Create.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ac144-124">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ac144-124">The preceding code:</span></span>

* <span data-ttu-id="ac144-125">Deriva de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ac144-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="ac144-126">Usa `TryUpdateModelAsync` para evitar la [publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ac144-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac144-127">Quita `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="ac144-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="ac144-128">`DepartmentNameSL` de la clase base es un modelo fuertemente tipado que usa la página de Razor.</span><span class="sxs-lookup"><span data-stu-id="ac144-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="ac144-129">Los modelos fuertemente tipados son preferibles a los de establecimiento flexible de tipos.</span><span class="sxs-lookup"><span data-stu-id="ac144-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="ac144-130">Para obtener más información, vea [Establecimiento flexible de datos (ViewData y ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="ac144-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="ac144-131">Actualización de la página de Razor Course Create</span><span class="sxs-lookup"><span data-stu-id="ac144-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="ac144-132">Actualice *Pages/Courses/Create.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="ac144-133">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac144-134">Se cambia el título de **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="ac144-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac144-135">Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).</span><span class="sxs-lookup"><span data-stu-id="ac144-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ac144-136">Se agrega la opción "Select Department" (Seleccionar departamento).</span><span class="sxs-lookup"><span data-stu-id="ac144-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="ac144-137">Este cambio representa "Select Department" (Seleccionar departamento) en la lista desplegable cuando todavía no se ha seleccionado ningún departamento, en lugar del primer departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="ac144-138">Se agrega un mensaje de validación cuando el departamento no está seleccionado.</span><span class="sxs-lookup"><span data-stu-id="ac144-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ac144-139">La página de Razor usa el [Asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="ac144-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="ac144-140">Pruebe la página Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-140">Test the Create page.</span></span> <span data-ttu-id="ac144-141">En la página Create se muestra el nombre del departamento en lugar del identificador.</span><span class="sxs-lookup"><span data-stu-id="ac144-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="ac144-142">Actualización del modelo de la página Course Edit</span><span class="sxs-lookup"><span data-stu-id="ac144-142">Update the Course Edit page model</span></span>

<span data-ttu-id="ac144-143">Actualice *Pages/Courses/Edit.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="ac144-144">Los cambios son similares a los realizados en el modelo de página de Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="ac144-145">En el código anterior, `PopulateDepartmentsDropDownList` pasa el identificador de departamento, que selecciona ese departamento en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="ac144-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="ac144-146">Actualización de la página de Razor Course Edit</span><span class="sxs-lookup"><span data-stu-id="ac144-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="ac144-147">Actualice *Pages/Courses/Edit.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="ac144-148">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac144-149">Se muestra el identificador del curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-149">Displays the course ID.</span></span> <span data-ttu-id="ac144-150">Por lo general no se muestra la clave principal (PK) de una entidad.</span><span class="sxs-lookup"><span data-stu-id="ac144-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="ac144-151">Las PK normalmente no tienen sentido para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="ac144-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="ac144-152">En este caso, la clave principal es el número de curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="ac144-153">Se cambia el título para la lista desplegable Department de **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="ac144-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac144-154">Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).</span><span class="sxs-lookup"><span data-stu-id="ac144-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac144-155">La página contiene un campo oculto (`<input type="hidden">`) para el número de curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="ac144-156">Agregar un asistente de etiquetas `<label>` con `asp-for="Course.CourseID"` no elimina la necesidad del campo oculto.</span><span class="sxs-lookup"><span data-stu-id="ac144-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="ac144-157">Se requiere `<input type="hidden">` para que el número de curso se incluya en los datos enviados cuando el usuario hace clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="ac144-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="ac144-158">Actualización de las páginas Course Details y Delete</span><span class="sxs-lookup"><span data-stu-id="ac144-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="ac144-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) puede mejorar el rendimiento cuando el seguimiento no es necesario.</span><span class="sxs-lookup"><span data-stu-id="ac144-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="ac144-160">Actualización de los modelos de página Course</span><span class="sxs-lookup"><span data-stu-id="ac144-160">Update the Course page models</span></span>

<span data-ttu-id="ac144-161">Actualice *Pages/Courses/Delete.cshtml.cs* con el código siguiente para agregar `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="ac144-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="ac144-162">Realice el mismo cambio en el archivo *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac144-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="ac144-163">Actualización de las páginas de Razor Course</span><span class="sxs-lookup"><span data-stu-id="ac144-163">Update the Course Razor pages</span></span>

<span data-ttu-id="ac144-164">Actualice *Pages/Courses/Delete.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="ac144-165">Realice los mismos cambios en la página Details.</span><span class="sxs-lookup"><span data-stu-id="ac144-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="ac144-166">Probar las páginas Course</span><span class="sxs-lookup"><span data-stu-id="ac144-166">Test the Course pages</span></span>

<span data-ttu-id="ac144-167">Pruebe las páginas Create, Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="ac144-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="ac144-168">Actualización de las páginas de creación y edición de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="ac144-169">Los instructores pueden impartir cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ac144-170">En la imagen siguiente se muestra la página de edición de instructores con una matriz de casillas de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="ac144-172">Las casillas permiten cambios en los cursos a los que está asignado un instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="ac144-173">Se muestra una casilla para cada curso en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ac144-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="ac144-174">Los cursos a los que el instructor está asignado se seleccionan.</span><span class="sxs-lookup"><span data-stu-id="ac144-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="ac144-175">El usuario puede activar o desactivar las casillas para cambiar las asignaciones de los cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="ac144-176">Si el número de cursos fuera mucho mayor, es posible que otra interfaz de usuario funcionara mejor.</span><span class="sxs-lookup"><span data-stu-id="ac144-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="ac144-177">Pero el método de administración de una relación de varios a varios que se muestra aquí no cambiaría.</span><span class="sxs-lookup"><span data-stu-id="ac144-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="ac144-178">Para crear o eliminar relaciones, se manipula una entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="ac144-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="ac144-179">Creación de una clase para los datos de los cursos asignados</span><span class="sxs-lookup"><span data-stu-id="ac144-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="ac144-180">Cree *SchoolViewModels/AssignedCourseData.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ac144-181">La clase `AssignedCourseData` contiene datos para crear las casillas para los cursos asignados a un instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="ac144-182">Creación de una clase base de modelo de páginas Instructor</span><span class="sxs-lookup"><span data-stu-id="ac144-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="ac144-183">Cree la clase base *Pages/Instructors/InstructorCoursesPageModel.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac144-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="ac144-184">`InstructorCoursesPageModel` es la clase base que se usará para los modelos de página de Edit y Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="ac144-185">`PopulateAssignedCourseData` lee todas las entidades `Course` para rellenar `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="ac144-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="ac144-186">Para cada curso, el código establece el `CourseID`, el título y si el instructor está asignado o no al curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="ac144-187">Se usa una instancia de [HashSet](/dotnet/api/system.collections.generic.hashset-1) para realizar búsquedas eficaces.</span><span class="sxs-lookup"><span data-stu-id="ac144-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="ac144-188">Puesto que la página de Razor no tiene una colección de entidades Course, el enlazador de modelos no puede actualizar automáticamente la propiedad de navegación `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ac144-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac144-189">En lugar de usar el enlazador de modelos para actualizar la propiedad de navegación `CourseAssignments`, lo hace en el nuevo método `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="ac144-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="ac144-190">Por lo tanto, tendrá que excluir la propiedad `CourseAssignments` del enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="ac144-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="ac144-191">Esto no requiere ningún cambio en el código que llama a `TryUpdateModel` porque está usando la sobrecarga con propiedades declaradas y `CourseAssignments` no se encuentra en la lista de inclusión.</span><span class="sxs-lookup"><span data-stu-id="ac144-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="ac144-192">Si no se ha seleccionado ninguna casilla, el código en `UpdateInstructorCourses` inicializa la propiedad de navegación `CourseAssignments` con una colección vacía y devuelve:</span><span class="sxs-lookup"><span data-stu-id="ac144-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="ac144-193">Después, el código recorre en bucle todos los cursos de la base de datos y comprueba los que están asignados actualmente al instructor con los que se han seleccionado en la página.</span><span class="sxs-lookup"><span data-stu-id="ac144-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="ac144-194">Para facilitar las búsquedas eficaces, estas dos últimas colecciones se almacenan en objetos `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="ac144-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="ac144-195">Si se ha activado la casilla para un curso pero este no se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se agrega a la colección en la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ac144-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="ac144-196">Si no se ha activado la casilla para un curso pero este se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se quita de la colección en la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ac144-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="ac144-197">Control de la ubicación de oficinas</span><span class="sxs-lookup"><span data-stu-id="ac144-197">Handle office location</span></span>

<span data-ttu-id="ac144-198">Otra relación que se tiene que controlar en la página de edición es la relación de uno a cero o uno que la entidad Instructor tiene con la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="ac144-199">El código de edición del instructor debe controlar los escenarios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="ac144-200">Si el usuario desactiva la asignación de la oficina, elimine la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac144-201">Si el usuario especifica una asignación de oficina y estaba vacía, cree una entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac144-202">Si el usuario cambia la asignación de oficina, actualice la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="ac144-203">Actualización del modelo de página de edición de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="ac144-204">Actualice *Pages/Instructors/Edit.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="ac144-205">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ac144-205">The preceding code:</span></span>

* <span data-ttu-id="ac144-206">Obtiene la entidad `Instructor` actual de la base de datos mediante la carga diligente de las propiedades de navegación `OfficeAssignment`, `CourseAssignment` y `CourseAssignment.Course`.</span><span class="sxs-lookup"><span data-stu-id="ac144-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="ac144-207">Actualiza la entidad `Instructor` recuperada con valores del enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="ac144-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="ac144-208">`TryUpdateModel` evita la [publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ac144-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac144-209">Si la ubicación de la oficina está en blanco, establece `Instructor.OfficeAssignment` en NULL.</span><span class="sxs-lookup"><span data-stu-id="ac144-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="ac144-210">Cuando `Instructor.OfficeAssignment` es NULL, se elimina la fila relacionada en la tabla `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="ac144-211">Llama a `PopulateAssignedCourseData` en `OnGetAsync` para proporcionar información de las casillas mediante la clase de modelo de vista `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="ac144-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="ac144-212">Llama a `UpdateInstructorCourses` en `OnPostAsync` para aplicar información de las casillas a la entidad Instructor que se va a editar.</span><span class="sxs-lookup"><span data-stu-id="ac144-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="ac144-213">Llama a `PopulateAssignedCourseData` y `UpdateInstructorCourses` en `OnPostAsync` si se produce un error en `TryUpdateModel`.</span><span class="sxs-lookup"><span data-stu-id="ac144-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="ac144-214">Estas llamadas de método restauran los datos de curso asignados que se escriben en la página cuando se vuelve a mostrar con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="ac144-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="ac144-215">Actualización de la página de Razor Instructor Edit</span><span class="sxs-lookup"><span data-stu-id="ac144-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="ac144-216">Actualice *Pages/Instructors/Edit.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="ac144-217">En el código anterior se crea una tabla HTML que tiene tres columnas.</span><span class="sxs-lookup"><span data-stu-id="ac144-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ac144-218">Cada columna tiene una casilla y una leyenda que contiene el número y el título del curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="ac144-219">Todas las casillas tienen el mismo nombre ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="ac144-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="ac144-220">Al usar el mismo nombre se informa al enlazador de modelos que las trate como un grupo.</span><span class="sxs-lookup"><span data-stu-id="ac144-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="ac144-221">El atributo de valor de cada casilla se establece en `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac144-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="ac144-222">Cuando se publica la página, el enlazador de modelos pasa una matriz formada solo por los valores `CourseID` de las casillas activadas.</span><span class="sxs-lookup"><span data-stu-id="ac144-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="ac144-223">Cuando se representan las casillas por primera vez, se seleccionan los cursos asignados al instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="ac144-224">Nota: El enfoque que se aplica aquí para modificar datos de los cursos del instructor funciona bien cuando hay un número limitado de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ac144-225">Para las colecciones que son mucho más grandes, una interfaz de usuario y un método de actualización diferentes serían más eficaces y útiles.</span><span class="sxs-lookup"><span data-stu-id="ac144-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="ac144-226">Ejecute la aplicación y pruebe la página de edición de instructores actualizada.</span><span class="sxs-lookup"><span data-stu-id="ac144-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="ac144-227">Cambie algunas asignaciones de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-227">Change some course assignments.</span></span> <span data-ttu-id="ac144-228">Los cambios se reflejan en la página Index.</span><span class="sxs-lookup"><span data-stu-id="ac144-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="ac144-229">Actualización de la página de creación de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-229">Update the Instructor Create page</span></span>

<span data-ttu-id="ac144-230">Actualice el modelo de la página Instructor Create y la página de Razor con código similar al de la página Edit:</span><span class="sxs-lookup"><span data-stu-id="ac144-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="ac144-231">Pruebe la página de creación de instructores.</span><span class="sxs-lookup"><span data-stu-id="ac144-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="ac144-232">Actualización de la página de eliminación de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="ac144-233">Actualice *Pages/Instructors/Delete.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="ac144-234">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac144-235">Se usa la carga diligente para la propiedad de navegación `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ac144-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac144-236">Es necesario incluir `CourseAssignments` o no se eliminarán cuando se elimine el instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="ac144-237">Para evitar la necesidad de leerlos, configure la eliminación en cascada en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ac144-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="ac144-238">Si el instructor que se va a eliminar está asignado como administrador de cualquiera de los departamentos, quita la asignación de instructor de esos departamentos.</span><span class="sxs-lookup"><span data-stu-id="ac144-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="ac144-239">Ejecute la aplicación y pruebe la página Delete.</span><span class="sxs-lookup"><span data-stu-id="ac144-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac144-240">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="ac144-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ac144-241">[Tutorial anterior](xref:data/ef-rp/read-related-data)
> [Tutorial siguiente](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="ac144-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac144-242">En este tutorial se muestra cómo actualizar datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="ac144-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="ac144-243">Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ac144-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="ac144-244">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ac144-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ac144-245">En las ilustraciones siguientes se muestran algunas de las páginas completadas.</span><span class="sxs-lookup"><span data-stu-id="ac144-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="ac144-246">![Página de edición de cursos](update-related-data/_static/course-edit.png)
![Página de edición de instructores](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="ac144-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="ac144-247">Examine y pruebe las páginas de cursos Create y Edit.</span><span class="sxs-lookup"><span data-stu-id="ac144-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="ac144-248">Cree un curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-248">Create a new course.</span></span> <span data-ttu-id="ac144-249">El departamento se selecciona por su clave principal (un entero), no su nombre.</span><span class="sxs-lookup"><span data-stu-id="ac144-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="ac144-250">Modifique el curso nuevo.</span><span class="sxs-lookup"><span data-stu-id="ac144-250">Edit the new course.</span></span> <span data-ttu-id="ac144-251">Cuando haya terminado las pruebas, elimine el curso nuevo.</span><span class="sxs-lookup"><span data-stu-id="ac144-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="ac144-252">Crear una clase base para compartir código común</span><span class="sxs-lookup"><span data-stu-id="ac144-252">Create a base class to share common code</span></span>

<span data-ttu-id="ac144-253">En las páginas Courses/Create y Courses/Edit se necesita una lista de nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="ac144-254">Cree la clase base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* para las páginas Create y Edit:</span><span class="sxs-lookup"><span data-stu-id="ac144-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="ac144-255">En el código anterior se crea una clase [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para que contenga la lista de nombres de departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="ac144-256">Si se especifica `selectedDepartment`, se selecciona ese departamento en la `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="ac144-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="ac144-257">Las clases de modelo de página de Create y Edit se derivan de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ac144-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="ac144-258">Personalizar las páginas de cursos</span><span class="sxs-lookup"><span data-stu-id="ac144-258">Customize the Courses Pages</span></span>

<span data-ttu-id="ac144-259">Cuando se crea una entidad de curso, debe tener una relación con un departamento existente.</span><span class="sxs-lookup"><span data-stu-id="ac144-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="ac144-260">Para agregar un departamento durante la creación de un curso, la clase base para Create y Edit contiene una lista desplegable para seleccionar el departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="ac144-261">La lista desplegable establece la propiedad de clave externa (FK) `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="ac144-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="ac144-262">EF Core usa la FK `Course.DepartmentID` para cargar la propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="ac144-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Crear el curso](update-related-data/_static/ddl.png)

<span data-ttu-id="ac144-264">Actualice el modelo de página de Create con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="ac144-265">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ac144-265">The preceding code:</span></span>

* <span data-ttu-id="ac144-266">Deriva de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="ac144-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="ac144-267">Usa `TryUpdateModelAsync` para evitar la [publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ac144-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac144-268">Reemplaza `ViewData["DepartmentID"]` con `DepartmentNameSL` (de la clase base).</span><span class="sxs-lookup"><span data-stu-id="ac144-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac144-269">`ViewData["DepartmentID"]` se reemplaza con `DepartmentNameSL` fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="ac144-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="ac144-270">Los modelos fuertemente tipados son preferibles a los de establecimiento flexible de tipos.</span><span class="sxs-lookup"><span data-stu-id="ac144-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="ac144-271">Para obtener más información, vea [Establecimiento flexible de datos (ViewData y ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="ac144-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="ac144-272">Actualizar la página Courses Create</span><span class="sxs-lookup"><span data-stu-id="ac144-272">Update the Courses Create page</span></span>

<span data-ttu-id="ac144-273">Actualice *Pages/Courses/Create.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="ac144-274">En el marcado anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ac144-275">Se cambia el título de **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="ac144-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac144-276">Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).</span><span class="sxs-lookup"><span data-stu-id="ac144-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ac144-277">Se agrega la opción "Select Department" (Seleccionar departamento).</span><span class="sxs-lookup"><span data-stu-id="ac144-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="ac144-278">Este cambio representa "Select Department" en lugar del primer departamento.</span><span class="sxs-lookup"><span data-stu-id="ac144-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="ac144-279">Se agrega un mensaje de validación cuando el departamento no está seleccionado.</span><span class="sxs-lookup"><span data-stu-id="ac144-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ac144-280">La página de Razor usa el [Asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="ac144-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="ac144-281">Pruebe la página Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-281">Test the Create page.</span></span> <span data-ttu-id="ac144-282">En la página Create se muestra el nombre del departamento en lugar del identificador.</span><span class="sxs-lookup"><span data-stu-id="ac144-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="ac144-283">Actualice la página Courses Edit.</span><span class="sxs-lookup"><span data-stu-id="ac144-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="ac144-284">Sustituya el código de *Pages/Courses/Edit.cshtml.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="ac144-285">Los cambios son similares a los realizados en el modelo de página de Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="ac144-286">En el código anterior, `PopulateDepartmentsDropDownList` pasa el identificador de departamento, que selecciona el departamento especificado en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="ac144-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="ac144-287">Actualice *Pages/Courses/Edit.cshtml* con el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="ac144-288">En el marcado anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ac144-289">Se muestra el identificador del curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-289">Displays the course ID.</span></span> <span data-ttu-id="ac144-290">Por lo general no se muestra la clave principal (PK) de una entidad.</span><span class="sxs-lookup"><span data-stu-id="ac144-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="ac144-291">Las PK normalmente no tienen sentido para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="ac144-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="ac144-292">En este caso, la clave principal es el número de curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="ac144-293">Se cambia el título de **DepartmentID** a **Department**.</span><span class="sxs-lookup"><span data-stu-id="ac144-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac144-294">Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).</span><span class="sxs-lookup"><span data-stu-id="ac144-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac144-295">La página contiene un campo oculto (`<input type="hidden">`) para el número de curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="ac144-296">Agregar un asistente de etiquetas `<label>` con `asp-for="Course.CourseID"` no elimina la necesidad del campo oculto.</span><span class="sxs-lookup"><span data-stu-id="ac144-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="ac144-297">Se requiere `<input type="hidden">` para que el número de curso se incluya en los datos enviados cuando el usuario hace clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="ac144-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="ac144-298">Pruebe el código actualizado.</span><span class="sxs-lookup"><span data-stu-id="ac144-298">Test the updated code.</span></span> <span data-ttu-id="ac144-299">Cree, modifique y elimine un curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="ac144-300">Agregar AsNoTracking a los modelos de página de Details y Delete</span><span class="sxs-lookup"><span data-stu-id="ac144-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="ac144-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) puede mejorar el rendimiento cuando el seguimiento no es necesario.</span><span class="sxs-lookup"><span data-stu-id="ac144-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="ac144-302">Agregue `AsNoTracking` al modelo de página de Delete y Details.</span><span class="sxs-lookup"><span data-stu-id="ac144-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="ac144-303">En el código siguiente se muestra el modelo de página de Delete actualizado:</span><span class="sxs-lookup"><span data-stu-id="ac144-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="ac144-304">Actualice el método `OnGetAsync` en el archivo *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac144-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="ac144-305">Modificar las páginas Delete y Details</span><span class="sxs-lookup"><span data-stu-id="ac144-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="ac144-306">Actualice la página de Razor Delete con el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="ac144-307">Realice los mismos cambios en la página Details.</span><span class="sxs-lookup"><span data-stu-id="ac144-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="ac144-308">Probar las páginas Course</span><span class="sxs-lookup"><span data-stu-id="ac144-308">Test the Course pages</span></span>

<span data-ttu-id="ac144-309">Pruebe las páginas Create, Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="ac144-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="ac144-310">Actualizar las páginas de instructor</span><span class="sxs-lookup"><span data-stu-id="ac144-310">Update the instructor pages</span></span>

<span data-ttu-id="ac144-311">En las siguientes secciones se actualizan las páginas de instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="ac144-312">Agregar la ubicación de la oficina</span><span class="sxs-lookup"><span data-stu-id="ac144-312">Add office location</span></span>

<span data-ttu-id="ac144-313">Al editar un registro de instructor, es posible que quiera actualizar la asignación de la oficina del instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="ac144-314">La entidad `Instructor` tiene una relación de uno a cero o uno con la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="ac144-315">El código de instructor debe controlar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-315">The instructor code must handle:</span></span>

* <span data-ttu-id="ac144-316">Si el usuario desactiva la asignación de la oficina, elimine la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac144-317">Si el usuario especifica una asignación de oficina y estaba vacía, cree una entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac144-318">Si el usuario cambia la asignación de oficina, actualice la entidad `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="ac144-319">Actualice el modelo de página de Edit de los instructores con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="ac144-320">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ac144-320">The preceding code:</span></span>

* <span data-ttu-id="ac144-321">Obtiene la entidad `Instructor` actual de la base de datos mediante la carga diligente de la propiedad de navegación `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="ac144-322">Actualiza la entidad `Instructor` recuperada con valores del enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="ac144-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="ac144-323">`TryUpdateModel` evita la [publicación excesiva](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="ac144-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac144-324">Si la ubicación de la oficina está en blanco, establece `Instructor.OfficeAssignment` en NULL.</span><span class="sxs-lookup"><span data-stu-id="ac144-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="ac144-325">Cuando `Instructor.OfficeAssignment` es NULL, se elimina la fila relacionada en la tabla `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="ac144-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="ac144-326">Actualizar la página Edit del instructor</span><span class="sxs-lookup"><span data-stu-id="ac144-326">Update the instructor Edit page</span></span>

<span data-ttu-id="ac144-327">Actualice *Pages/Instructors/Edit.cshtml* con la ubicación de la oficina:</span><span class="sxs-lookup"><span data-stu-id="ac144-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="ac144-328">Compruebe que puede cambiar la ubicación de la oficina de un instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="ac144-329">Agregar asignaciones de cursos a la página Edit de los instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="ac144-330">Los instructores pueden impartir cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ac144-331">En esta sección, agregará la capacidad de cambiar las asignaciones de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="ac144-332">En la imagen siguiente se muestra la página Edit actualizada de los instructores:</span><span class="sxs-lookup"><span data-stu-id="ac144-332">The following image shows the updated instructor Edit page:</span></span>

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="ac144-334">`Course` e `Instructor` tienen una relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="ac144-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="ac144-335">Para agregar y eliminar relaciones, agregue y quite entidades del conjunto de entidades combinadas `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ac144-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="ac144-336">Las casillas permiten cambios en los cursos a los que está asignado un instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="ac144-337">Se muestra una casilla para cada curso en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ac144-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="ac144-338">Los cursos a los que el instructor está asignado se activan.</span><span class="sxs-lookup"><span data-stu-id="ac144-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="ac144-339">El usuario puede activar o desactivar las casillas para cambiar las asignaciones de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="ac144-340">Si el número de cursos fuera mucho mayor:</span><span class="sxs-lookup"><span data-stu-id="ac144-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="ac144-341">Probablemente usaría una interfaz de usuario diferente para mostrar los cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="ac144-342">El método de manipulación de una entidad de combinación para crear o eliminar relaciones no cambiaría.</span><span class="sxs-lookup"><span data-stu-id="ac144-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="ac144-343">Agregar clases para admitir las páginas de instructor Create y Edit</span><span class="sxs-lookup"><span data-stu-id="ac144-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="ac144-344">Cree *SchoolViewModels/AssignedCourseData.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ac144-345">La clase `AssignedCourseData` contiene datos para crear las casillas para los cursos asignados por un instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="ac144-346">Cree la clase base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac144-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="ac144-347">`InstructorCoursesPageModel` es la clase base que se usará para los modelos de página de Edit y Create.</span><span class="sxs-lookup"><span data-stu-id="ac144-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="ac144-348">`PopulateAssignedCourseData` lee todas las entidades `Course` para rellenar `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="ac144-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="ac144-349">Para cada curso, el código establece el `CourseID`, el título y si el instructor está asignado o no al curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="ac144-350">Se usa un [HashSet](/dotnet/api/system.collections.generic.hashset-1) para crear búsquedas eficaces.</span><span class="sxs-lookup"><span data-stu-id="ac144-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="ac144-351">Modelo de página de edición de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-351">Instructors Edit page model</span></span>

<span data-ttu-id="ac144-352">Actualice el modelo de página de edición de instructores con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="ac144-353">El código anterior controla los cambios de asignación de oficina.</span><span class="sxs-lookup"><span data-stu-id="ac144-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="ac144-354">Actualice la vista de Razor Instructor:</span><span class="sxs-lookup"><span data-stu-id="ac144-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="ac144-355">Al pegar el código en Visual Studio, se cambian los saltos de línea de tal forma que el código se interrumpe.</span><span class="sxs-lookup"><span data-stu-id="ac144-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="ac144-356">Presione Ctrl+Z una vez para deshacer el formato automático.</span><span class="sxs-lookup"><span data-stu-id="ac144-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="ac144-357">Ctrl+Z corrige los saltos de línea para que se muestren como se ven aquí.</span><span class="sxs-lookup"><span data-stu-id="ac144-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="ac144-358">No es necesario que la sangría sea perfecta, pero las líneas `@:</tr><tr>`, `@:<td>`, `@:</td>` y `@:</tr>` deben estar en una única línea tal y como se muestra.</span><span class="sxs-lookup"><span data-stu-id="ac144-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="ac144-359">Con el bloque de código nuevo seleccionado, presione tres veces la tecla Tab para alinearlo con el código existente.</span><span class="sxs-lookup"><span data-stu-id="ac144-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="ac144-360">Puede votar o revisar el estado de este error [con este vínculo](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="ac144-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="ac144-361">En el código anterior se crea una tabla HTML que tiene tres columnas.</span><span class="sxs-lookup"><span data-stu-id="ac144-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ac144-362">Cada columna tiene una casilla y una leyenda que contiene el número y el título del curso.</span><span class="sxs-lookup"><span data-stu-id="ac144-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="ac144-363">Todas las casillas tienen el mismo nombre ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="ac144-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="ac144-364">Al usar el mismo nombre se informa al enlazador de modelos que las trate como un grupo.</span><span class="sxs-lookup"><span data-stu-id="ac144-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="ac144-365">El atributo de valor de cada casilla se establece en `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac144-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="ac144-366">Cuando se envía la página, el enlazador de modelos pasa una matriz formada solo por los valores `CourseID` de las casillas activadas.</span><span class="sxs-lookup"><span data-stu-id="ac144-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="ac144-367">Cuando se representan las casillas por primera vez, los cursos asignados al instructor tienen atributos checked.</span><span class="sxs-lookup"><span data-stu-id="ac144-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="ac144-368">Ejecute la aplicación y pruebe la página Edit de los instructores actualizada.</span><span class="sxs-lookup"><span data-stu-id="ac144-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="ac144-369">Cambie algunas asignaciones de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-369">Change some course assignments.</span></span> <span data-ttu-id="ac144-370">Los cambios se reflejan en la página Index.</span><span class="sxs-lookup"><span data-stu-id="ac144-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="ac144-371">Nota: El enfoque que se aplica aquí para modificar datos de los cursos del instructor funciona bien cuando hay un número limitado de cursos.</span><span class="sxs-lookup"><span data-stu-id="ac144-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ac144-372">Para las colecciones que son mucho más grandes, una interfaz de usuario y un método de actualización diferentes serían más eficaces y útiles.</span><span class="sxs-lookup"><span data-stu-id="ac144-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="ac144-373">Actualizar la página de creación de instructores</span><span class="sxs-lookup"><span data-stu-id="ac144-373">Update the instructors Create page</span></span>

<span data-ttu-id="ac144-374">Actualice el modelo de página de creación de instructores con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="ac144-375">El código anterior es similar al de *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac144-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="ac144-376">Actualice la página de Razor Instructor Create con el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="ac144-377">Pruebe la página de creación de instructores.</span><span class="sxs-lookup"><span data-stu-id="ac144-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="ac144-378">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="ac144-378">Update the Delete page</span></span>

<span data-ttu-id="ac144-379">Actualice el modelo de la página Delete con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac144-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="ac144-380">En el código anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac144-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac144-381">Se usa la carga diligente para la propiedad de navegación `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="ac144-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac144-382">Es necesario incluir `CourseAssignments` o no se eliminarán cuando se elimine el instructor.</span><span class="sxs-lookup"><span data-stu-id="ac144-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="ac144-383">Para evitar la necesidad de leerlos, configure la eliminación en cascada en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ac144-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="ac144-384">Si el instructor que se va a eliminar está asignado como administrador de cualquiera de los departamentos, quita la asignación de instructor de esos departamentos.</span><span class="sxs-lookup"><span data-stu-id="ac144-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac144-385">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ac144-385">Additional resources</span></span>

* [<span data-ttu-id="ac144-386">Versión en YouTube de este tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="ac144-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="ac144-387">Versión en YouTube de este tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="ac144-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="ac144-388">[Anterior](xref:data/ef-rp/read-related-data)
> [Siguiente](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="ac144-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
