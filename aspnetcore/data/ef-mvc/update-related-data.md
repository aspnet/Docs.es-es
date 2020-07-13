---
title: 'Tutorial: Actualización de datos relacionados: ASP.NET MVC con EF Core'
description: En este tutorial, actualizará datos relacionados mediante la actualización de campos de clave externa y propiedades de navegación.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 066bebf95a941fca5e7cc175c4c0d6d56abc9cb5
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060064"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Actualización de datos relacionados: ASP.NET MVC con EF Core

En el tutorial anterior, mostró los datos relacionados; en este tutorial, actualizará los datos relacionados mediante la actualización de campos de clave externa y las propiedades de navegación.

En las ilustraciones siguientes se muestran algunas de las páginas con las que va a trabajar.

![Página Course Edit](update-related-data/_static/course-edit.png)

![Página Instructor Edit](update-related-data/_static/instructor-edit-courses.png)

En este tutorial ha:

> [!div class="checklist"]
> * Personaliza las páginas de cursos
> * Agrega la página de edición de instructores
> * Agrega cursos a la página de edición
> * Actualiza la página Delete
> * Agrega la ubicación de la oficina y cursos a la página Create

## <a name="prerequisites"></a>Requisitos previos

* [Lectura de datos relacionados](read-related-data.md)

## <a name="customize-courses-pages"></a>Personaliza las páginas de cursos

Cuando se crea una entidad de curso, debe tener una relación con un departamento existente. Para facilitar esto, el código con scaffolding incluye métodos de controlador y vistas de Create y Edit que incluyen una lista desplegable para seleccionar el departamento. La lista desplegable establece la propiedad de clave externa de `Course.DepartmentID`, y eso es todo lo que necesita de Entity Framework para cargar la propiedad de navegación de `Department` con la entidad Department adecuada. Podrá usar el código con scaffolding, pero cámbielo ligeramente para agregar el control de errores y ordenar la lista desplegable.

En *CoursesController.cs*, elimine los cuatro métodos de creación y edición, y reemplácelos con el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Después del método HttpPost de `Edit`, cree un método que cargue la información de departamento para la lista desplegable.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

El método `PopulateDepartmentsDropDownList` obtiene una lista de todos los departamentos ordenados por nombre, crea una colección `SelectList` para obtener una lista desplegable y pasa la colección a la vista en `ViewBag`. El método acepta el parámetro opcional `selectedDepartment`, que permite al código que realiza la llamada especificar el elemento que se seleccionará cuando se procese la lista desplegable. La vista pasará el nombre "DepartmentID" al asistente de etiquetas `<select>`, y luego el asistente sabe que puede buscar en el objeto `ViewBag` una `SelectList` denominada "DepartmentID".

El método `Create` de HttpGet llama al método `PopulateDepartmentsDropDownList` sin configurar el elemento seleccionado, ya que el departamento todavía no está establecido para un nuevo curso:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

El método `Edit` de HttpGet establece el elemento seleccionado, basándose en el identificador del departamento que ya está asignado a la línea que se está editando:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Los métodos HttpPost para `Create` y `Edit` también incluyen código que configura el elemento seleccionado cuando vuelven a mostrar la página después de un error. Esto garantiza que, cuando vuelve a aparecer la página para mostrar el mensaje de error, el departamento que se haya seleccionado permanece seleccionado.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Agregar AsNoTracking a los métodos Details y Delete

Para optimizar el rendimiento de las páginas Course Details y Delete, agregue llamadas `AsNoTracking` en los métodos `Details` y `Delete` de HttpGet.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modificar las vistas de Course

En *Views/Courses/Create.cshtml*, agregue una opción "Select Department" a la lista desplegable **Department**, cambie el título de **DepartmentID** a  **Department** y agregue un mensaje de validación.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

En *Views/Courses/Edit.cshtml*, realice el mismo cambio que acaba de hacer en *Create.cshtml* en el campo Department.

También en *Views/Courses/Edit.cshtml*, agregue un campo de número de curso antes del campo **Title**. Dado que el número de curso es la clave principal, esta se muestra, pero no se puede cambiar.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Ya hay un campo oculto (`<input type="hidden">`) para el número de curso en la vista Edit. Agregar un asistente de etiquetas `<label>` no elimina la necesidad de un campo oculto, porque no hace que el número de curso se incluya en los datos enviados cuando el usuario hace clic en **Save** en la página **Edit**.

En *Views/Courses/Delete.cshtml*, agregue un campo de número de curso en la parte superior y cambie el identificador del departamento por el nombre del departamento.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

En *Views/Courses/Details.cshtml*, realice el mismo cambio que acaba de hacer en *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Probar las páginas Course

Ejecute la aplicación, seleccione la pestaña **Courses**, haga clic en **Create New** y escriba los datos del curso nuevo:

![Página Course Create](update-related-data/_static/course-create.png)

Haga clic en **Crear**. Se muestra la página de índice de cursos con el nuevo curso agregado a la lista. El nombre de departamento de la lista de páginas de índice proviene de la propiedad de navegación, que muestra que la relación se estableció correctamente.

Haga clic en **Edit** en un curso en la página de índice de cursos.

![Página Course Edit](update-related-data/_static/course-edit.png)

Cambie los datos en la página y haga clic en **Save**. Se muestra la página de índice de cursos con los datos del curso actualizados.

## <a name="add-instructors-edit-page"></a>Agrega la página de edición de instructores

Al editar un registro de instructor, necesita poder actualizar la asignación de la oficina del instructor. La entidad Instructor tiene una relación de uno a cero o uno con la entidad OfficeAssignment, lo que significa que el código tiene que controlar las situaciones siguientes:

* Si el usuario borra la asignación de oficina y esta tenía originalmente un valor, elimine la entidad OfficeAssignment.

* Si el usuario escribe un valor de asignación de oficina y originalmente estaba vacío, cree una entidad OfficeAssignment.

* Si el usuario cambia el valor de una asignación de oficina, cambie el valor en una entidad OfficeAssignment existente.

### <a name="update-the-instructors-controller"></a>Actualizar el controlador de Instructors

En *InstructorsController.cs*, cambie el código en el método `Edit` de HttpGet para que cargue la propiedad de navegación `OfficeAssignment` de la entidad Instructor y llame a `AsNoTracking`:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Reemplace el método `Edit` de HttpPost con el siguiente código para controlar las actualizaciones de asignaciones de oficina:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

El código realiza lo siguiente:

* Cambia el nombre del método a `EditPost` porque la firma ahora es la misma que el método `Edit` de HttpGet (el atributo `ActionName` especifica que la dirección URL de `/Edit/` aún está en uso).

* Obtiene la entidad Instructor actual de la base de datos mediante la carga diligente de la propiedad de navegación `OfficeAssignment`. Esto es lo mismo que hizo en el método `Edit` de HttpGet.

* Actualiza la entidad Instructor recuperada con valores del enlazador de modelos. La sobrecarga de `TryUpdateModel` le permite declarar las propiedades que quiera incluir. Esto evita el registro excesivo, como se explica en el [segundo tutorial](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Si la ubicación de la oficina está en blanco, establece la propiedad Instructor.OfficeAssignment en NULL para que se elimine la fila relacionada en la tabla OfficeAssignment.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Guarda los cambios en la base de datos.

### <a name="update-the-instructor-edit-view"></a>Actualizar la vista de Edit de Instructor

En *Views/Instructors/Edit.cshtml*, agregue un nuevo campo para editar la ubicación de la oficina, al final antes del botón **Save**:

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Ejecute la aplicación, seleccione la pestaña **Instructors** y, después, haga clic en **Edit** en un instructor. Cambie el valor de **Office Location** y haga clic en **Save**.

![Página Instructor Edit](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Agrega cursos a la página de edición

Los instructores pueden impartir cualquier número de cursos. Ahora mejorará la página Edit Instructor al agregar la capacidad de cambiar las asignaciones de cursos mediante un grupo de casillas, tal y como se muestra en la siguiente captura de pantalla:

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses.png)

La relación entre las entidades Course e Instructor es varios a varios. Para agregar y eliminar relaciones, agregue y quite entidades del conjunto de entidades combinadas CourseAssignments.

La interfaz de usuario que le permite cambiar los cursos a los que está asignado un instructor es un grupo de casillas. Se muestra una casilla para cada curso en la base de datos y se seleccionan aquellos a los que está asignado actualmente el instructor. El usuario puede activar o desactivar las casillas para cambiar las asignaciones de cursos. Si el número de cursos fuera mucho mayor, probablemente tendría que usar un método diferente de presentar los datos en la vista, pero usaría el mismo método de manipulación de una entidad de combinación para crear o eliminar relaciones.

### <a name="update-the-instructors-controller"></a>Actualizar el controlador de Instructors

Para proporcionar datos a la vista de la lista de casillas, deberá usar una clase de modelo de vista.

Cree *AssignedCourseData.cs* en la carpeta *SchoolViewModels* y reemplace el código existente con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

En *InstructorsController.cs*, reemplace el método `Edit` de HttpGet por el código siguiente. Los cambios aparecen resaltados.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

El código agrega carga diligente para la propiedad de navegación `Courses` y llama al método `PopulateAssignedCourseData` nuevo para proporcionar información de la matriz de casilla mediante la clase de modelo de vista `AssignedCourseData`.

El código en el método `PopulateAssignedCourseData` lee a través de todas las entidades Course para cargar una lista de cursos mediante la clase de modelo de vista. Para cada curso, el código comprueba si existe el curso en la propiedad de navegación `Courses` del instructor. Para crear una búsqueda eficaz al comprobar si un curso está asignado al instructor, los cursos asignados a él se colocan en una colección `HashSet`. La propiedad `Assigned` está establecida en true para los cursos a los que está asignado el instructor. La vista usará esta propiedad para determinar qué casilla debe mostrarse como seleccionada. Por último, la lista se pasa a la vista en `ViewData`.

A continuación, agregue el código que se ejecuta cuando el usuario hace clic en **Save**. Reemplace el método `EditPost` con el siguiente código y agregue un nuevo método que actualiza la propiedad de navegación `Courses` de la entidad Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

La firma del método ahora es diferente del método `Edit` de HttpGet, por lo que el nombre del método cambia de `EditPost` a `Edit`.

Puesto que la vista no tiene una colección de entidades Course, el enlazador de modelos no puede actualizar automáticamente la propiedad de navegación `CourseAssignments`. En lugar de usar el enlazador de modelos para actualizar la propiedad de navegación `CourseAssignments`, lo hace en el nuevo método `UpdateInstructorCourses`. Por lo tanto, tendrá que excluir la propiedad `CourseAssignments` del enlace de modelos. Esto no requiere ningún cambio en el código que llama a `TryUpdateModel` porque está usando la sobrecarga que requiere aprobación explícita y `CourseAssignments` no está en la lista de inclusión.

Si no se ha seleccionado ninguna casilla, el código en `UpdateInstructorCourses` inicializa la propiedad de navegación `CourseAssignments` con una colección vacía y devuelve:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

A continuación, el código recorre en bucle todos los cursos de la base de datos y coteja los que están asignados actualmente al instructor frente a los que se han seleccionado en la vista. Para facilitar las búsquedas eficaces, estas dos últimas colecciones se almacenan en objetos `HashSet`.

Si se ha activado la casilla para un curso pero este no se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se agrega a la colección en la propiedad de navegación.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Si no se ha activado la casilla para un curso pero este se encuentra en la propiedad de navegación `Instructor.CourseAssignments`, el curso se quita de la colección en la propiedad de navegación.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Actualizar las vistas de Instructor

En *Views/Instructors/Edit.cshtml*, agregue un campo **Courses** con una matriz de casillas al agregar el siguiente código inmediatamente después de los elementos `div` del campo **Office** y antes del elemento `div` del botón **Save**.

<a id="notepad"></a>
> [!NOTE]
> Al pegar el código en Visual Studio, los saltos de línea podrían cambiarse de tal forma que el código se interrumpiese. Si el código tiene un aspecto diferente después de pegarlo, presione CTRL+Z una vez para deshacer el formato automático. Esto corregirá los saltos de línea para que se muestren como se ven aquí. No es necesario que la sangría sea perfecta, pero las líneas `@:</tr><tr>`, `@:<td>`, `@:</td>` y `@:</tr>` deben estar en una única línea tal y como se muestra, de lo contrario, obtendrá un error en tiempo de ejecución. Con el bloque de código nuevo seleccionado, presione tres veces la tecla TAB para alinearlo con el código existente. Este problema se ha corregido en Visual Studio 2019.

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Este código crea una tabla HTML que tiene tres columnas. En cada columna hay una casilla seguida de un título que está formado por el número y el título del curso. Todas las casillas tienen el mismo nombre ("selectedCourses"), que informa al enlazador de modelos que se deben tratar como un grupo. El atributo de valor de cada casilla se establece en el valor de `CourseID`. Cuando se envía la página, el enlazador de modelos pasa una matriz al controlador formada solo por los valores `CourseID` de las casillas activadas.

Cuando las casillas se representan inicialmente, aquellas que son para cursos asignados al instructor tienen atributos seleccionados, lo que las selecciona (las muestra activadas).

Ejecute la aplicación, seleccione la pestaña **Instructors** y haga clic en **Edit** en un instructor para ver la página **Edit**.

![Página de edición de instructores con cursos](update-related-data/_static/instructor-edit-courses.png)

Cambie algunas asignaciones de cursos y haga clic en Save. Los cambios que haga se reflejan en la página de índice.

> [!NOTE]
> El enfoque que se aplica aquí para modificar datos de los cursos del instructor funciona bien cuando hay un número limitado de cursos. Para las colecciones que son mucho más grandes, se necesitaría una interfaz de usuario y un método de actualización diferentes.

## <a name="update-delete-page"></a>Actualiza la página Delete

En *InstructorsController.cs*, elimine el método `DeleteConfirmed` e inserte el siguiente código en su lugar.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Este código realiza los cambios siguientes:

* Hace la carga diligente para la propiedad de navegación `CourseAssignments`. Tiene que incluir esto o EF no conocerá las entidades `CourseAssignment` relacionadas y no las eliminará. Para evitar la necesidad de leerlos aquí, puede configurar la eliminación en cascada en la base de datos.

* Si el instructor que se va a eliminar está asignado como administrador de cualquiera de los departamentos, quita la asignación de instructor de esos departamentos.

## <a name="add-office-location-and-courses-to-create-page"></a>Agrega la ubicación de la oficina y cursos a la página Create

En *InstructorsController.cs*, elimine los métodos `Create` de HttpGet y HttpPost y, después, agregue el código siguiente en su lugar:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Este código es similar a lo que ha visto para los métodos `Edit`, excepto que no hay cursos seleccionados inicialmente. El método `Create` de HttpGet no llama al método `PopulateAssignedCourseData` porque pueda haber cursos seleccionados sino para proporcionar una colección vacía para el bucle `foreach` en la vista (en caso contrario, el código de vista podría producir una excepción de referencia nula).

El método `Create` de HttpPost agrega cada curso seleccionado a la propiedad de navegación `CourseAssignments` antes de comprobar si hay errores de validación y agrega el instructor nuevo a la base de datos. Los cursos se agregan incluso si hay errores de modelo, por lo que cuando hay errores del modelo (por ejemplo, el usuario escribió una fecha no válida) y se vuelve a abrir la página con un mensaje de error, las selecciones de cursos que se habían realizado se restauran todas automáticamente.

Tenga en cuenta que, para poder agregar cursos a la propiedad de navegación `CourseAssignments`, debe inicializar la propiedad como una colección vacía:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Como alternativa a hacerlo en el código de control, podría hacerlo en el modelo de Instructor cambiando el captador de propiedad para que cree automáticamente la colección si no existe, como se muestra en el ejemplo siguiente:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Si modifica la propiedad `CourseAssignments` de esta manera, puede quitar el código de inicialización de propiedad explícito del controlador.

En *Views/Instructor/Create.cshtml*, agregue un cuadro de texto de la ubicación de la oficina y casillas para cursos antes del botón Submit. Al igual que en el caso de la página Edit, [corrija el formato si Visual Studio vuelve a aplicar formato al código al pegarlo](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Pruebe a ejecutar la aplicación y crear un instructor.

## <a name="handling-transactions"></a>Control de transacciones

Como se explicó en el [tutorial de CRUD](crud.md), Entity Framework implementa las transacciones de manera implícita. Para escenarios donde se necesita más control, por ejemplo, si se quieren incluir operaciones realizadas fuera de Entity Framework en una transacción, vea [Transacciones](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Personalizado las páginas de cursos
> * Agregado la página de edición de instructores
> * Agregado cursos a la página de edición
> * Actualizado la página Delete
> * Agregado la ubicación de la oficina y cursos a la página Create

Pase al tutorial siguiente para obtener información sobre cómo controlar conflictos de simultaneidad.

> [!div class="nextstepaction"]
> [Control de conflictos de simultaneidad](concurrency.md)
