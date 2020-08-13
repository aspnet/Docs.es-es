---
title: 'Parte 7. Razor Pages con EF Core en ASP.NET Core: Actualización de datos relacionados'
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre Razor Pages y Entity Framework.
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3807c52bb843c4d6403e8236fde50c034a8d1e2b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017746"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Parte 7. Razor Pages con EF Core en ASP.NET Core: Actualización de datos relacionados

Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

En este tutorial se muestra cómo actualizar datos relacionados. En las ilustraciones siguientes se muestran algunas de las páginas completadas.

![Página de edición de cursos](update-related-data/_static/course-edit30.png)
![Página de edición de instructores](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Actualización de las páginas de creación y edición de cursos

El código con scaffolding para las páginas Course Create y Edit tiene una lista desplegable Department en la que se muestra el identificador del departamento (un entero). En la lista desplegable se debe mostrar el nombre del departamento, de modo que en las dos páginas se necesita una lista de nombres de departamento. Para proporcionar esa lista, use una clase base para las páginas Create y Edit.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Creación de una clase base para las páginas de creación y edición de cursos

Cree un archivo *Pages/Courses/DepartmentNamePageModel.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

En el código anterior se crea una clase [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para que contenga la lista de nombres de departamento. Si se especifica `selectedDepartment`, se selecciona ese departamento en la `SelectList`.

Las clases de modelo de página de Create y Edit se derivan de `DepartmentNamePageModel`.

### <a name="update-the-course-create-page-model"></a>Actualización del modelo de la página Course Create

Una entidad Course (Curso) se asigna a una entidad Department (Departamento). La clase base para las páginas Create y Edit proporciona un elemento `SelectList` para seleccionar el departamento. La lista desplegable que usa el elemento `SelectList` establece la propiedad de clave externa (FK) `Course.DepartmentID`. EF Core usa la FK `Course.DepartmentID` para cargar la propiedad de navegación `Department`.

![Crear el curso](update-related-data/_static/ddl30.png)

Actualice *Pages/Courses/Create.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

El código anterior:

* Deriva de `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` para evitar la [publicación excesiva](xref:data/ef-rp/crud#overposting).
* Quita `ViewData["DepartmentID"]`. `DepartmentNameSL` de la clase base es un modelo fuertemente tipado que usa la página de Razor. Los modelos fuertemente tipados son preferibles a los de establecimiento flexible de tipos. Para obtener más información, vea [Establecimiento flexible de datos (ViewData y ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Actualización de la página de Razor Course Create

Actualice *Pages/Courses/Create.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

En el código anterior se realizan los cambios siguientes:

* Se cambia el título de **DepartmentID** a **Department**.
* Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).
* Se agrega la opción "Select Department" (Seleccionar departamento). Este cambio representa "Select Department" (Seleccionar departamento) en la lista desplegable cuando todavía no se ha seleccionado ningún departamento, en lugar del primer departamento.
* Se agrega un mensaje de validación cuando el departamento no está seleccionado.

La página de Razor usa el [Asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Pruebe la página Create. En la página Create se muestra el nombre del departamento en lugar del identificador.

### <a name="update-the-course-edit-page-model"></a>Actualización del modelo de la página Course Edit

Actualice *Pages/Courses/Edit.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Los cambios son similares a los realizados en el modelo de página de Create. En el código anterior, `PopulateDepartmentsDropDownList` pasa el identificador de departamento, que selecciona ese departamento en la lista desplegable.

### <a name="update-the-course-edit-no-locrazor-page"></a>Actualización de la página de Razor Course Edit

Actualice *Pages/Courses/Edit.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

En el código anterior se realizan los cambios siguientes:

* Se muestra el identificador del curso. Por lo general no se muestra la clave principal (PK) de una entidad. Las PK normalmente no tienen sentido para los usuarios. En este caso, la clave principal es el número de curso.
* Se cambia el título para la lista desplegable Department de **DepartmentID** a **Department**.
* Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).

La página contiene un campo oculto (`<input type="hidden">`) para el número de curso. Agregar un asistente de etiquetas `<label>` con `asp-for="Course.CourseID"` no elimina la necesidad del campo oculto. Se requiere `<input type="hidden">` para que el número de curso se incluya en los datos enviados cuando el usuario hace clic en **Guardar**.

## <a name="update-the-course-details-and-delete-pages"></a>Actualización de las páginas Course Details y Delete

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) puede mejorar el rendimiento cuando el seguimiento no es necesario.

### <a name="update-the-course-page-models"></a>Actualización de los modelos de página Course

Actualice *Pages/Courses/Delete.cshtml.cs* con el código siguiente para agregar `AsNoTracking`:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Realice el mismo cambio en el archivo *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Actualización de las páginas de Razor Course

Actualice *Pages/Courses/Delete.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Realice los mismos cambios en la página Details.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Probar las páginas Course

Pruebe las páginas Create, Edit, Details y Delete.

## <a name="update-the-instructor-create-and-edit-pages"></a>Actualización de las páginas de creación y edición de instructores

Los instructores pueden impartir cualquier número de cursos. En la imagen siguiente se muestra la página de edición de instructores con una matriz de casillas de cursos.

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses30.png)

Las casillas permiten cambios en los cursos a los que está asignado un instructor. Se muestra una casilla para cada curso en la base de datos. Los cursos a los que el instructor está asignado se seleccionan. El usuario puede activar o desactivar las casillas para cambiar las asignaciones de los cursos. Si el número de cursos fuera mucho mayor, es posible que otra interfaz de usuario funcionara mejor. Pero el método de administración de una relación de varios a varios que se muestra aquí no cambiaría. Para crear o eliminar relaciones, se manipula una entidad de combinación.

### <a name="create-a-class-for-assigned-courses-data"></a>Creación de una clase para los datos de los cursos asignados

Cree *SchoolViewModels/AssignedCourseData.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

La clase `AssignedCourseData` contiene datos para crear las casillas para los cursos asignados a un instructor.

### <a name="create-an-instructor-page-model-base-class"></a>Creación de una clase base de modelo de páginas Instructor

Cree la clase base *Pages/Instructors/InstructorCoursesPageModel.cs*:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel` es la clase base que se usará para los modelos de página de Edit y Create. `PopulateAssignedCourseData` lee todas las entidades `Course` para rellenar `AssignedCourseDataList`. Para cada curso, el código establece el `CourseID`, el título y si el instructor está asignado o no al curso. Se usa una instancia de [HashSet](/dotnet/api/system.collections.generic.hashset-1) para realizar búsquedas eficaces.

Puesto que la página de Razor no tiene una colección de entidades Course, el enlazador de modelos no puede actualizar automáticamente la propiedad de navegación `CourseAssignments`. En lugar de usar el enlazador de modelos para actualizar la propiedad de navegación `CourseAssignments`, lo hace en el nuevo método `UpdateInstructorCourses`. Por lo tanto, tendrá que excluir la propiedad `CourseAssignments` del enlace de modelos. Esto no requiere ningún cambio en el código que llama a `TryUpdateModel` porque está usando la sobrecarga con propiedades declaradas y `CourseAssignments` no se encuentra en la lista de inclusión.

Si no se ha seleccionado ninguna casilla, el código en `UpdateInstructorCourses` inicializa la propiedad de navegación `CourseAssignments` con una colección vacía y devuelve:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Después, el código recorre en bucle todos los cursos de la base de datos y comprueba los que están asignados actualmente al instructor con los que se han seleccionado en la página. Para facilitar las búsquedas eficaces, estas dos últimas colecciones se almacenan en objetos `HashSet`.

Si se ha activado la casilla para un curso pero este no se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se agrega a la colección en la propiedad de navegación.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Si no se ha activado la casilla para un curso pero este se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se quita de la colección en la propiedad de navegación.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Control de la ubicación de oficinas

Otra relación que se tiene que controlar en la página de edición es la relación de uno a cero o uno que la entidad Instructor tiene con la entidad `OfficeAssignment`. El código de edición del instructor debe controlar los escenarios siguientes: 

* Si el usuario desactiva la asignación de la oficina, elimine la entidad `OfficeAssignment`.
* Si el usuario especifica una asignación de oficina y estaba vacía, cree una entidad `OfficeAssignment`.
* Si el usuario cambia la asignación de oficina, actualice la entidad `OfficeAssignment`.

### <a name="update-the-instructor-edit-page-model"></a>Actualización del modelo de página de edición de instructores

Actualice *Pages/Instructors/Edit.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

El código anterior:

* Obtiene la entidad `Instructor` actual de la base de datos mediante la carga diligente de las propiedades de navegación `OfficeAssignment`, `CourseAssignment` y `CourseAssignment.Course`.
* Actualiza la entidad `Instructor` recuperada con valores del enlazador de modelos. `TryUpdateModel` evita la [publicación excesiva](xref:data/ef-rp/crud#overposting).
* Si la ubicación de la oficina está en blanco, establece `Instructor.OfficeAssignment` en NULL. Cuando `Instructor.OfficeAssignment` es NULL, se elimina la fila relacionada en la tabla `OfficeAssignment`.
* Llama a `PopulateAssignedCourseData` en `OnGetAsync` para proporcionar información de las casillas mediante la clase de modelo de vista `AssignedCourseData`.
* Llama a `UpdateInstructorCourses` en `OnPostAsync` para aplicar información de las casillas a la entidad Instructor que se va a editar.
* Llama a `PopulateAssignedCourseData` y `UpdateInstructorCourses` en `OnPostAsync` si se produce un error en `TryUpdateModel`. Estas llamadas de método restauran los datos de curso asignados que se escriben en la página cuando se vuelve a mostrar con un mensaje de error.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Actualización de la página de Razor Instructor Edit

Actualice *Pages/Instructors/Edit.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

En el código anterior se crea una tabla HTML que tiene tres columnas. Cada columna tiene una casilla y una leyenda que contiene el número y el título del curso. Todas las casillas tienen el mismo nombre ("selectedCourses"). Al usar el mismo nombre se informa al enlazador de modelos que las trate como un grupo. El atributo de valor de cada casilla se establece en `CourseID`. Cuando se publica la página, el enlazador de modelos pasa una matriz formada solo por los valores `CourseID` de las casillas activadas.

Cuando se representan las casillas por primera vez, se seleccionan los cursos asignados al instructor.

Nota: El enfoque que se aplica aquí para modificar datos de los cursos del instructor funciona bien cuando hay un número limitado de cursos. Para las colecciones que son mucho más grandes, una interfaz de usuario y un método de actualización diferentes serían más eficaces y útiles.

Ejecute la aplicación y pruebe la página de edición de instructores actualizada. Cambie algunas asignaciones de cursos. Los cambios se reflejan en la página Index.

### <a name="update-the-instructor-create-page"></a>Actualización de la página de creación de instructores

Actualice el modelo de la página Instructor Create y la página de Razor con código similar al de la página Edit:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Pruebe la página de creación de instructores.

## <a name="update-the-instructor-delete-page"></a>Actualización de la página de eliminación de instructores

Actualice *Pages/Instructors/Delete.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

En el código anterior se realizan los cambios siguientes:

* Se usa la carga diligente para la propiedad de navegación `CourseAssignments`. Es necesario incluir `CourseAssignments` o no se eliminarán cuando se elimine el instructor. Para evitar la necesidad de leerlos, configure la eliminación en cascada en la base de datos.

* Si el instructor que se va a eliminar está asignado como administrador de cualquiera de los departamentos, quita la asignación de instructor de esos departamentos.

Ejecute la aplicación y pruebe la página Delete.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/read-related-data)
> [Tutorial siguiente](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial se muestra cómo actualizar datos relacionados. Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instrucciones de descarga](xref:index#how-to-download-a-sample).

En las ilustraciones siguientes se muestran algunas de las páginas completadas.

![Página de edición de cursos](update-related-data/_static/course-edit.png)
![Página de edición de instructores](update-related-data/_static/instructor-edit-courses.png)

Examine y pruebe las páginas de cursos Create y Edit. Cree un curso. El departamento se selecciona por su clave principal (un entero), no su nombre. Modifique el curso nuevo. Cuando haya terminado las pruebas, elimine el curso nuevo.

## <a name="create-a-base-class-to-share-common-code"></a>Crear una clase base para compartir código común

En las páginas Courses/Create y Courses/Edit se necesita una lista de nombres de departamento. Cree la clase base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* para las páginas Create y Edit:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

En el código anterior se crea una clase [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para que contenga la lista de nombres de departamento. Si se especifica `selectedDepartment`, se selecciona ese departamento en la `SelectList`.

Las clases de modelo de página de Create y Edit se derivan de `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personalizar las páginas de cursos

Cuando se crea una entidad de curso, debe tener una relación con un departamento existente. Para agregar un departamento durante la creación de un curso, la clase base para Create y Edit contiene una lista desplegable para seleccionar el departamento. La lista desplegable establece la propiedad de clave externa (FK) `Course.DepartmentID`. EF Core usa la FK `Course.DepartmentID` para cargar la propiedad de navegación `Department`.

![Crear el curso](update-related-data/_static/ddl.png)

Actualice el modelo de página de Create con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

El código anterior:

* Deriva de `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` para evitar la [publicación excesiva](xref:data/ef-rp/crud#overposting).
* Reemplaza `ViewData["DepartmentID"]` con `DepartmentNameSL` (de la clase base).

`ViewData["DepartmentID"]` se reemplaza con `DepartmentNameSL` fuertemente tipado. Los modelos fuertemente tipados son preferibles a los de establecimiento flexible de tipos. Para obtener más información, vea [Establecimiento flexible de datos (ViewData y ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Actualizar la página Courses Create

Actualice *Pages/Courses/Create.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

En el marcado anterior se realizan los cambios siguientes:

* Se cambia el título de **DepartmentID** a **Department**.
* Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).
* Se agrega la opción "Select Department" (Seleccionar departamento). Este cambio representa "Select Department" en lugar del primer departamento.
* Se agrega un mensaje de validación cuando el departamento no está seleccionado.

La página de Razor usa el [Asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Pruebe la página Create. En la página Create se muestra el nombre del departamento en lugar del identificador.

### <a name="update-the-courses-edit-page"></a>Actualice la página Courses Edit.

Sustituya el código de *Pages/Courses/Edit.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Los cambios son similares a los realizados en el modelo de página de Create. En el código anterior, `PopulateDepartmentsDropDownList` pasa el identificador de departamento, que selecciona el departamento especificado en la lista desplegable.

Actualice *Pages/Courses/Edit.cshtml* con el marcado siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

En el marcado anterior se realizan los cambios siguientes:

* Se muestra el identificador del curso. Por lo general no se muestra la clave principal (PK) de una entidad. Las PK normalmente no tienen sentido para los usuarios. En este caso, la clave principal es el número de curso.
* Se cambia el título de **DepartmentID** a **Department**.
* Se reemplaza `"ViewBag.DepartmentID"` con `DepartmentNameSL` (de la clase base).

La página contiene un campo oculto (`<input type="hidden">`) para el número de curso. Agregar un asistente de etiquetas `<label>` con `asp-for="Course.CourseID"` no elimina la necesidad del campo oculto. Se requiere `<input type="hidden">` para que el número de curso se incluya en los datos enviados cuando el usuario hace clic en **Guardar**.

Pruebe el código actualizado. Cree, modifique y elimine un curso.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Agregar AsNoTracking a los modelos de página de Details y Delete

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) puede mejorar el rendimiento cuando el seguimiento no es necesario. Agregue `AsNoTracking` al modelo de página de Delete y Details. En el código siguiente se muestra el modelo de página de Delete actualizado:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Actualice el método `OnGetAsync` en el archivo *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modificar las páginas Delete y Details

Actualice la página de Razor Delete con el marcado siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Realice los mismos cambios en la página Details.

### <a name="test-the-course-pages"></a>Probar las páginas Course

Pruebe las páginas Create, Edit, Details y Delete.

## <a name="update-the-instructor-pages"></a>Actualizar las páginas de instructor

En las siguientes secciones se actualizan las páginas de instructor.

### <a name="add-office-location"></a>Agregar la ubicación de la oficina

Al editar un registro de instructor, es posible que quiera actualizar la asignación de la oficina del instructor. La entidad `Instructor` tiene una relación de uno a cero o uno con la entidad `OfficeAssignment`. El código de instructor debe controlar lo siguiente:

* Si el usuario desactiva la asignación de la oficina, elimine la entidad `OfficeAssignment`.
* Si el usuario especifica una asignación de oficina y estaba vacía, cree una entidad `OfficeAssignment`.
* Si el usuario cambia la asignación de oficina, actualice la entidad `OfficeAssignment`.

Actualice el modelo de página de Edit de los instructores con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

El código anterior:

* Obtiene la entidad `Instructor` actual de la base de datos mediante la carga diligente de la propiedad de navegación `OfficeAssignment`.
* Actualiza la entidad `Instructor` recuperada con valores del enlazador de modelos. `TryUpdateModel` evita la [publicación excesiva](xref:data/ef-rp/crud#overposting).
* Si la ubicación de la oficina está en blanco, establece `Instructor.OfficeAssignment` en NULL. Cuando `Instructor.OfficeAssignment` es NULL, se elimina la fila relacionada en la tabla `OfficeAssignment`.

### <a name="update-the-instructor-edit-page"></a>Actualizar la página Edit del instructor

Actualice *Pages/Instructors/Edit.cshtml* con la ubicación de la oficina:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Compruebe que puede cambiar la ubicación de la oficina de un instructor.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Agregar asignaciones de cursos a la página Edit de los instructores

Los instructores pueden impartir cualquier número de cursos. En esta sección, agregará la capacidad de cambiar las asignaciones de cursos. En la imagen siguiente se muestra la página Edit actualizada de los instructores:

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses.png)

`Course` e `Instructor` tienen una relación de varios a varios. Para agregar y eliminar relaciones, agregue y quite entidades del conjunto de entidades combinadas `CourseAssignments`.

Las casillas permiten cambios en los cursos a los que está asignado un instructor. Se muestra una casilla para cada curso en la base de datos. Los cursos a los que el instructor está asignado se activan. El usuario puede activar o desactivar las casillas para cambiar las asignaciones de cursos. Si el número de cursos fuera mucho mayor:

* Probablemente usaría una interfaz de usuario diferente para mostrar los cursos.
* El método de manipulación de una entidad de combinación para crear o eliminar relaciones no cambiaría.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Agregar clases para admitir las páginas de instructor Create y Edit

Cree *SchoolViewModels/AssignedCourseData.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

La clase `AssignedCourseData` contiene datos para crear las casillas para los cursos asignados por un instructor.

Cree la clase base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` es la clase base que se usará para los modelos de página de Edit y Create. `PopulateAssignedCourseData` lee todas las entidades `Course` para rellenar `AssignedCourseDataList`. Para cada curso, el código establece el `CourseID`, el título y si el instructor está asignado o no al curso. Se usa un [HashSet](/dotnet/api/system.collections.generic.hashset-1) para crear búsquedas eficaces.

### <a name="instructors-edit-page-model"></a>Modelo de página de edición de instructores

Actualice el modelo de página de edición de instructores con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

El código anterior controla los cambios de asignación de oficina.

Actualice la vista de Razor Instructor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Al pegar el código en Visual Studio, se cambian los saltos de línea de tal forma que el código se interrumpe. Presione Ctrl+Z una vez para deshacer el formato automático. Ctrl+Z corrige los saltos de línea para que se muestren como se ven aquí. No es necesario que la sangría sea perfecta, pero las líneas `@:</tr><tr>`, `@:<td>`, `@:</td>` y `@:</tr>` deben estar en una única línea tal y como se muestra. Con el bloque de código nuevo seleccionado, presione tres veces la tecla Tab para alinearlo con el código existente. Puede votar o revisar el estado de este error [con este vínculo](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

En el código anterior se crea una tabla HTML que tiene tres columnas. Cada columna tiene una casilla y una leyenda que contiene el número y el título del curso. Todas las casillas tienen el mismo nombre ("selectedCourses"). Al usar el mismo nombre se informa al enlazador de modelos que las trate como un grupo. El atributo de valor de cada casilla se establece en `CourseID`. Cuando se envía la página, el enlazador de modelos pasa una matriz formada solo por los valores `CourseID` de las casillas activadas.

Cuando se representan las casillas por primera vez, los cursos asignados al instructor tienen atributos checked.

Ejecute la aplicación y pruebe la página Edit de los instructores actualizada. Cambie algunas asignaciones de cursos. Los cambios se reflejan en la página Index.

Nota: El enfoque que se aplica aquí para modificar datos de los cursos del instructor funciona bien cuando hay un número limitado de cursos. Para las colecciones que son mucho más grandes, una interfaz de usuario y un método de actualización diferentes serían más eficaces y útiles.

### <a name="update-the-instructors-create-page"></a>Actualizar la página de creación de instructores

Actualice el modelo de página de creación de instructores con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

El código anterior es similar al de *Pages/Instructors/Edit.cshtml.cs*.

Actualice la página de Razor Instructor Create con el marcado siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Pruebe la página de creación de instructores.

## <a name="update-the-delete-page"></a>Actualizar la página Delete

Actualice el modelo de la página Delete con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

En el código anterior se realizan los cambios siguientes:

* Se usa la carga diligente para la propiedad de navegación `CourseAssignments`. Es necesario incluir `CourseAssignments` o no se eliminarán cuando se elimine el instructor. Para evitar la necesidad de leerlos, configure la eliminación en cascada en la base de datos.

* Si el instructor que se va a eliminar está asignado como administrador de cualquiera de los departamentos, quita la asignación de instructor de esos departamentos.

## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial (parte 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Versión en YouTube de este tutorial (parte 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/read-related-data)
> [Siguiente](xref:data/ef-rp/concurrency)

::: moniker-end
