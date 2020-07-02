---
title: 'Tutorial: Lectura de datos relacionados: ASP.NET MVC con EF Core'
description: En este tutorial podrá leer y mostrar datos relacionados, es decir, los datos que Entity Framework carga en propiedades de navegación.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: f2e989b2c6370d862b4d1e6550b09cb47b5747c1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401524"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Lectura de datos relacionados: ASP.NET MVC con EF Core

En el tutorial anterior, completó el modelo de datos School. En este tutorial podrá leer y mostrar datos relacionados, es decir, los datos que Entity Framework carga en propiedades de navegación.

En las ilustraciones siguientes se muestran las páginas con las que va a trabajar.

![Página de índice de cursos](read-related-data/_static/courses-index.png)

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

En este tutorial ha:

> [!div class="checklist"]
> * Obtiene información sobre cómo cargar datos relacionados
> * Crea una página de cursos
> * Crea una página de instructores
> * Obtiene información sobre la carga explícita

## <a name="prerequisites"></a>Requisitos previos

* [Creación de un modelo de datos complejo](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Obtiene información sobre cómo cargar datos relacionados

Existen varias formas para que el software de asignación relacional de objetos (ORM) como Entity Framework pueda cargar datos relacionados en las propiedades de navegación de una entidad:

* Carga diligente. Cuando se lee la entidad, junto a ella se recuperan datos relacionados. Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan. En Entity Framework Core, la carga diligente se especifica mediante los métodos `Include` y `ThenInclude`.

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)

  Puede recuperar algunos de los datos en distintas consultas y EF "corregirá" las propiedades de navegación.  Es decir, EF agrega automáticamente las entidades recuperadas por separado que pertenecen a propiedades de navegación de entidades recuperadas previamente. Para la consulta que recupera los datos relacionados, puede usar el método `Load` en lugar de un método que devuelva una lista o un objeto, como `ToList` o `Single`.

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

* Carga explícita. Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. Se escribe código que recupera los datos relacionados si son necesarios. Como en el caso de la carga diligente con consultas independientes, la carga explícita da como resultado varias consultas que se envían a la base de datos. La diferencia es que con la carga explícita el código especifica las propiedades de navegación que se van a cargar. En Entity Framework Core 1.1 se puede usar el método `Load` para realizar la carga explícita. Por ejemplo:

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* Carga diferida. Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. Pero la primera vez que intente obtener acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación. Cada vez que intente obtener datos de una propiedad de navegación por primera vez, se envía una consulta a la base de datos. Entity Framework Core 1.0 no admite la carga diferida.

### <a name="performance-considerations"></a>Consideraciones sobre el rendimiento

Si sabe que necesita datos relacionados para cada entidad que se recupere, la carga diligente suele ofrecer el mejor rendimiento, dado que una sola consulta que se envía a la base de datos normalmente es más eficaz que consultas independientes para cada entidad recuperada. Por ejemplo, suponga que cada departamento tiene diez cursos relacionados. Con la carga diligente de todos los datos relacionados se crearía una única consulta sencilla (de combinación) y un único recorrido de ida y vuelta a la base de datos. Una consulta independiente para los cursos de cada departamento crearía 11 recorridos de ida y vuelta a la base de datos. Los recorridos de ida y vuelta adicionales a la base de datos afectan especialmente de forma negativa al rendimiento cuando la latencia es alta.

Por otro lado, en algunos escenarios, las consultas independientes son más eficaces. Es posible que la carga diligente de todos los datos relacionados en una consulta genere una combinación muy compleja que SQL Server no pueda procesar eficazmente. O bien, si necesita tener acceso a las propiedades de navegación de una entidad solo para un subconjunto de un conjunto de las entidades que está procesando, es posible que las consultas independientes den mejores resultados porque la carga diligente de todo el contenido por adelantado recuperaría más datos de los que necesita. Si el rendimiento es crítico, es mejor probarlo de ambas formas para elegir la mejor opción.

## <a name="create-a-courses-page"></a>Crea una página de cursos

La entidad Course incluye una propiedad de navegación que contiene la entidad Department del departamento al que se asigna el curso. Para mostrar el nombre del departamento asignado en una lista de cursos, tendrá que obtener la propiedad Name de la entidad Department que se encuentra en la propiedad de navegación `Course.Department`.

Cree un controlador denominado CoursesController para el tipo de entidad Course, con las mismas opciones para el proveedor de scaffolding **Controlador de MVC con vistas que usan Entity Framework** que usó anteriormente para el controlador de Students, como se muestra en la ilustración siguiente:

![Agregar el controlador de cursos](read-related-data/_static/add-courses-controller.png)

Abra *CoursesController.cs* y examine el método `Index`. El scaffolding automático ha especificado la carga diligente para la propiedad de navegación `Department` mediante el método `Include`.

Reemplace el método `Index` con el siguiente código, en el que se usa un nombre más adecuado para la `IQueryable` que devuelve las entidades Course (`courses` en lugar de `schoolContext`):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Abra *Views/Courses/Index.cshtml* y reemplace el código de plantilla con el código siguiente. Se resaltan los cambios:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Ha realizado los cambios siguientes en el código con scaffolding:

* Ha cambiado el título de Index a Courses.

* Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`. De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales. Pero en este caso, la clave principal es significativa y quiere mostrarla.

* Ha cambiado la columna **Department** para mostrar el nombre del departamento. El código muestra la propiedad `Name` de la entidad Department que se carga en la propiedad de navegación `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.

![Página de índice de cursos](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Crea una página de instructores

En esta sección, creará un controlador y una vista de la entidad Instructor con el fin de mostrar la página Instructors:

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

En esta página se leen y muestran los datos relacionados de las maneras siguientes:

* En la lista de instructores se muestran datos relacionados de la entidad OfficeAssignment. Las entidades Instructor y OfficeAssignment se encuentran en una relación de uno a cero o uno. Usará la carga diligente para las entidades OfficeAssignment. Como se explicó anteriormente, la carga diligente normalmente es más eficaz cuando se necesitan los datos relacionados para todas las filas recuperadas de la tabla principal. En este caso, quiere mostrar las asignaciones de oficina para todos los instructores que se muestran.

* Cuando el usuario selecciona un instructor, se muestran las entidades Course relacionadas. Las entidades Instructor y Course se encuentran en una relación de varios a varios. Usará la carga diligente para las entidades Course y sus entidades Department relacionadas. En este caso, es posible que las consultas independientes sean más eficaces porque necesita cursos solo para el instructor seleccionado. Pero en este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación dentro de entidades que, a su vez, se encuentran en propiedades de navegación.

* Cuando el usuario selecciona un curso, se muestran los datos relacionados del conjunto de entidades Enrollments. Las entidades Course y Enrollment están en una relación uno a varios. Usará consultas independientes para las entidades Enrollment y sus entidades Student relacionadas.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Crear un modelo de vista para la vista de índice de instructores

En la página Instructors se muestran datos de tres tablas diferentes. Por tanto, creará un modelo de vista que incluye tres propiedades, cada una con los datos de una de las tablas.

En la carpeta *SchoolViewModels*, cree *InstructorIndexData.cs* y reemplace el código existente con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Crear el controlador y las vistas de Instructor

Cree un controlador Instructors con acciones de lectura y escritura de EF como se muestra en la ilustración siguiente:

![Adición del controlador de instructores](read-related-data/_static/add-instructors-controller.png)

Abra *InstructorsController.cs* y agregue una instrucción using para el espacio de nombres ViewModels:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Reemplace el método Index con el código siguiente para realizar la carga diligente de los datos relacionados y colocarlos en el modelo de vista.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

El método acepta datos de ruta opcionales (`id`) y un parámetro de cadena de consulta (`courseID`) que proporcionan los valores ID del instructor y el curso seleccionados. Los parámetros se proporcionan mediante los hipervínculos **Select** de la página.

El código comienza creando una instancia del modelo de vista y coloca en ella la lista de instructores. El código especifica la carga diligente para `Instructor.OfficeAssignment` y las propiedades de navegación de `Instructor.CourseAssignments`. Dentro de la propiedad `CourseAssignments` se carga la propiedad `Course` y dentro de esta se cargan las propiedades `Enrollments` y `Department`, y dentro de cada entidad `Enrollment` se carga la propiedad `Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Como la vista siempre requiere la entidad OfficeAssignment, resulta más eficaz capturarla en la misma consulta. Las entidades Course son necesarias cuando se selecciona un instructor en la página web, por lo que una sola consulta es más adecuada que varias solo si la página se muestra con más frecuencia con un curso seleccionado que sin él.

El código repite `CourseAssignments` y `Course` porque se necesitan dos propiedades de `Course`. En la primera cadena de llamadas `ThenInclude` se obtiene `CourseAssignment.Course`, `Course.Enrollments` y `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

En ese punto del código, otro elemento `ThenInclude` sería para las propiedades de navegación de `Student`, lo que no es necesario. Pero la llamada a `Include` se inicia con las propiedades de `Instructor`, por lo que tendrá que volver a pasar por la cadena, especificando esta vez `Course.Department` en lugar de `Course.Enrollments`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

El código siguiente se ejecuta cuando se ha seleccionado un instructor. El instructor seleccionado se recupera de la lista de instructores del modelo de vista. Después, se carga la propiedad `Courses` del modelo de vista con las entidades Course de la propiedad de navegación `CourseAssignments` de ese instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

El método `Where` devuelve una colección, pero en este caso los criterios que se pasan a ese método dan como resultado que solo se devuelva una entidad Instructor. El método `Single` convierte la colección en una única entidad Instructor, que proporciona acceso a la propiedad `CourseAssignments` de esa entidad. La propiedad `CourseAssignments` contiene entidades `CourseAssignment`, de las que solo quiere las entidades `Course` relacionadas.

El método `Single` se usa en una colección cuando se sabe que la colección tendrá un único elemento. El método Single inicia una excepción si la colección que se pasa está vacía o si hay más de un elemento. Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía. Pero en este caso, eso seguiría iniciando una excepción (al tratar de buscar una propiedad `Courses` en una referencia nula), y el mensaje de excepción indicaría con menos claridad la causa del problema. Cuando se llama al método `Single`, también se puede pasar la condición Where en lugar de llamar al método `Where` por separado:

```csharp
.Single(i => i.ID == id.Value)
```

En lugar de:

```csharp
.Where(i => i.ID == id.Value).Single()
```

A continuación, si se ha seleccionado un curso, se recupera de la lista de cursos en el modelo de vista. Después, se carga la propiedad `Enrollments` del modelo de vista con las entidades Enrollment de la propiedad de navegación `Enrollments` de ese curso.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modificar la vista de índice de instructores

En *Views/Instructors/Index.cshtml*, reemplace el código de plantilla con el código siguiente. Los cambios aparecen resaltados.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Ha realizado los cambios siguientes en el código existente:

* Ha cambiado la clase de modelo por `InstructorIndexData`.

* Ha cambiado el título de la página de **Index** a **Instructors**.

* Se ha agregado una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL. (Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada).

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Se ha agregado una columna **Courses** en la que se muestran los cursos que imparte cada instructor. Para obtener más información, vea la sección [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition) del artículo sobre la sintaxis de Razor.

* Ha agregado código que agrega dinámicamente `class="success"` al elemento `tr` del instructor seleccionado. Esto establece el color de fondo de la fila seleccionada mediante una clase de arranque.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Se ha agregado un hipervínculo nuevo con la etiqueta **Select** inmediatamente antes de los otros vínculos de cada fila, lo que hace que el identificador del instructor seleccionado se envíe al método `Index`.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la propiedad Location de las entidades OfficeAssignment relacionadas y una celda de tabla vacía cuando no hay ninguna entidad OfficeAssignment relacionada.

![Página de índice de instructores sin ninguna selección](read-related-data/_static/instructors-index-no-selection.png)

En el archivo *Views/Instructors/Index.cshtml*, después del elemento de tabla de cierre (situado al final del archivo), agregue el código siguiente. Este código muestra una lista de cursos relacionados con un instructor cuando se selecciona un instructor.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Este código lee la propiedad `Courses` del modelo de vista para mostrar una lista de cursos. También proporciona un hipervínculo **Select** que envía el identificador del curso seleccionado al método de acción `Index`.

Actualice la página y seleccione un instructor. Ahora verá una cuadrícula en la que se muestran los cursos asignados al instructor seleccionado, y para cada curso, el nombre del departamento asignado.

![Instructor seleccionado en la página de índice de instructores](read-related-data/_static/instructors-index-instructor-selected.png)

Después del bloque de código que se acaba de agregar, agregue el código siguiente. Esto muestra una lista de los estudiantes que están inscritos en un curso cuando se selecciona ese curso.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Este código lee la propiedad Enrollments del modelo de vista para mostrar una lista de los estudiantes inscritos en el curso.

Vuelva a actualizar la página y seleccione un instructor. Después, seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Acerca de la carga explícita

Cuando se recuperó la lista de instructores en *InstructorsController.cs*, se especificó la carga diligente de la propiedad de navegación `CourseAssignments`.

Suponga que esperaba que los usuarios rara vez quisieran ver las inscripciones en un instructor y curso seleccionados. En ese caso, es posible que quiera cargar los datos de inscripción solo si se solicitan. Para ver un ejemplo de cómo realizar la carga explícita, reemplace el método `Index` con el código siguiente, que quita la carga diligente de Enrollments y carga explícitamente esa propiedad. Los cambios de código aparecen resaltados.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

El nuevo código quita las llamadas al método *ThenInclude* para los datos de inscripción del código que recupera las entidades Instructor. También quita `AsNoTracking`.  Si se seleccionan un instructor y un curso, el código resaltado recupera las entidades Enrollment para el curso seleccionado y las entidades Student de cada inscripción.

Ejecute la aplicación, vaya a la página de índice de instructores ahora y no verá ninguna diferencia en lo que se muestra en la página, aunque haya cambiado la forma en que se recuperan los datos.

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Obtenido información sobre cómo cargar datos relacionados
> * Creado una página de cursos
> * Creado una página de instructores
> * Obtenido información sobre la carga explícita

Pase al tutorial siguiente para obtener información sobre cómo actualizar datos relacionados.

> [!div class="nextstepaction"]
> [Actualización de datos relacionados](update-related-data.md)
