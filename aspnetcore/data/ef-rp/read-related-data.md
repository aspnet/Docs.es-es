---
title: 'Parte 6. Razor Pages con EF Core en ASP.NET Core: Lectura de datos relacionados'
author: rick-anderson
description: Parte 6 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061072"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Parte 6. Razor Pages con EF Core en ASP.NET Core: Lectura de datos relacionados

Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) y [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

En este tutorial se muestra cómo leer y mostrar datos relacionados. Los datos relacionados son los que EF Core carga en las propiedades de navegación.

En las ilustraciones siguientes se muestran las páginas completadas para este tutorial:

![Página de índice de cursos](read-related-data/_static/courses-index30.png)

![Página de índice de instructores](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Carga diligente, explícita y diferida

EF Core puede cargar datos relacionados en las propiedades de navegación de una entidad de varias maneras:

* [Carga diligente](/ef/core/querying/related-data#eager-loading). La carga diligente es cuando una consulta para un tipo de entidad también carga las entidades relacionadas. Cuando se lee una entidad, se recuperan sus datos relacionados. Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan. EF Core emitirá varias consultas para algunos tipos de carga diligente. La emisión de varias consultas puede ser más eficaz que una sola consulta gigante. La carga diligente se especifica con los métodos `Include` y `ThenInclude`.

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)
 
  La carga diligente envía varias consultas cuando se incluye una propiedad de navegación de colección:

  * Una consulta para la consulta principal 
  * Una consulta para cada colección "perimetral" en el árbol de la carga.

* Separe las consultas con `Load`: los datos se pueden recuperar en distintas consultas y EF Core "corrige" las propiedades de navegación. "Corregir" significa que EF Core rellena de forma automática las propiedades de navegación. La separación de las consultas con `Load` es más parecido a la carga explícita que a la carga diligente.

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

  **Nota:** EF Core corrige automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Incluso si los datos de una propiedad de navegación *no* se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.

* [Carga explícita](/ef/core/querying/related-data#explicit-loading). Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. Se debe escribir código para recuperar los datos relacionados cuando sea necesario. La carga explícita con consultas independientes da como resultado varias consultas que se envían a la base de datos. Con la carga explícita, el código especifica las propiedades de navegación que se van a cargar. Use el método `Load` para realizar la carga explícita. Por ejemplo:

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* [Carga diferida](/ef/core/querying/related-data#lazy-loading). Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. La primera vez que se obtiene acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación. Cada vez que se accede por primera vez a una propiedad de navegación, se envía una consulta a la base de datos. La carga diferida puede afectar negativamente al rendimiento, por ejemplo, cuando los desarrolladores usan patrones N+1, al cargar un elemento primario y enumerar los elementos secundarios.

## <a name="create-course-pages"></a>Creación de las páginas Course

La entidad `Course` incluye una propiedad de navegación que contiene la entidad `Department` relacionada.

![Course.Department](read-related-data/_static/dep-crs.png)

Para mostrar el nombre del departamento asignado a un curso:

* Cargue la entidad `Department` relacionada en la propiedad de navegación `Course.Department`.
* Obtenga el nombre de la propiedad `Name` de la entidad `Department`.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Scaffolding de las páginas Course

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:

  * Cree una carpeta *Pages/Courses*.
  * Use `Course` para la clase del modelo.
  * Use la clase de contexto existente en lugar de crear una.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Cree una carpeta *Pages/Courses*.

* Ejecute el comando siguiente para aplicar scaffolding a las páginas Course.

  **En Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **En Linux o macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Abra *Pages/Courses/Index.cshtml.cs* y examine el método `OnGetAsync`. El motor de scaffolding especificado realiza la carga diligente de la propiedad de navegación `Department`. El método `Include` especifica la carga diligente.

* Ejecute la aplicación y haga clic en el vínculo **Courses**. En la columna Department se muestra el `DepartmentID`, lo que no resulta útil.

### <a name="display-the-department-name"></a>Representación del nombre del departamento

Actualice Pages/Courses/Index.cshtml.cs con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

En el código anterior se cambia la propiedad `Course` a `Courses` y se agrega `AsNoTracking`. `AsNoTracking` mejora el rendimiento porque no se realiza el seguimiento de las entidades devueltas. No es necesario realizar el seguimiento de las entidades porque no se actualizan en el contexto actual.

Actualice *Pages/Courses/Index.cshtml* con el código siguiente.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

Se han realizado los cambios siguientes en el código con scaffolding:

* Se ha cambiado el nombre de la propiedad `Course` a `Courses`.
* Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`. De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales. Pero en este caso, la clave principal es significativa.
* Ha cambiado la columna **Department** para mostrar el nombre del departamento. El código muestra la propiedad `Name` de la entidad `Department` que se carga en la propiedad de navegación `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.

![Página de índice de cursos](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Carga de datos relacionados con Select

El método `OnGetAsync` carga los datos relacionados con el método `Include`. El método `Select` es una alternativa que solo carga los datos relacionados necesarios. Para elementos individuales, como el `Department.Name`, se usa INNER JOIN de SQL. En las colecciones, se usa otro acceso de base de datos, como también hace el operador `Include` en las colecciones.

En el código siguiente se cargan los datos relacionados con el método `Select`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

El código anterior no devuelve ningún tipo de entidad, por lo que no se realiza ningún seguimiento. Para más información sobre el seguimiento de EF, consulte [Consultas de seguimiento frente a consultas de no seguimiento](/ef/core/querying/tracking).

El `CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Vea [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) para obtener un ejemplo completo.

## <a name="create-instructor-pages"></a>Creación de las páginas Instructor

Esta sección se aplica scaffolding a las páginas Instructor y se agregan entidades Course y Enrollment a la página Instructors Index (índice de instructores).

<a name="IP"></a>
![Página de índice de instructores](read-related-data/_static/instructors-index30.png)

En esta página se leen y muestran los datos relacionados de las maneras siguientes:

* En la lista de instructores se muestran datos relacionados de la entidad `OfficeAssignment` (Office en la imagen anterior). Las entidades `Instructor` y `OfficeAssignment` se encuentran en una relación de uno a cero o uno. Para las entidades `OfficeAssignment` se usa la carga diligente. Normalmente la carga diligente es más eficaz cuando es necesario mostrar los datos relacionados. En este caso, se muestran las asignaciones de oficina para los instructores.
* Cuando el usuario selecciona un instructor, se muestran las entidades `Course` relacionadas. Las entidades `Instructor` y `Course` se encuentran en una relación de varios a varios. La carga diligente se usa con las entidades `Course` y sus entidades `Department` relacionadas. En este caso, es posible que las consultas independientes sean más eficaces porque solo se necesitan cursos para el instructor seleccionado. En este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación en entidades que se encuentran en propiedades de navegación.
* Cuando el usuario selecciona un curso, se muestran los datos relacionados de la entidad `Enrollments`. En la imagen anterior, se muestra el nombre del alumno y la calificación. Las entidades `Course` y `Enrollment` se encuentran en una relación uno a varios.

### <a name="create-a-view-model"></a>Creación de un modelo de vista

En la página Instructors se muestran datos de tres tablas diferentes. Se necesita un modelo de vista que incluya tres entidades que representen las tres tablas.

Cree *SchoolViewModels/InstructorIndexData.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Scaffolding de las páginas Instructor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:

  * Cree una carpeta *Pages/Instructors*.
  * Use `Instructor` para la clase del modelo.
  * Use la clase de contexto existente en lugar de crear una.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Cree una carpeta *Pages/Instructors*.

* Ejecute el comando siguiente para aplicar scaffolding a las páginas Instructor.

  **En Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **En Linux o macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Para ver el aspecto de la página con scaffolding antes de actualizarla, ejecute la aplicación y vaya a la página de instructores.

Actualice *Pages/Instructors/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

El método `OnGetAsync` acepta datos de ruta opcionales para el identificador del instructor seleccionado.

Examine la consulta en el archivo *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

El código especifica la carga diligente para las propiedades de navegación siguientes:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Observe la repetición de los métodos `Include` y `ThenInclude` para `CourseAssignments` y `Course`. Esta repetición es necesaria para especificar la carga diligente para dos propiedades de navegación de la entidad `Course`.

El código siguiente se ejecuta cuando se selecciona un instructor (`id != null`).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

El instructor seleccionado se recupera de la lista de instructores del modelo de vista. Se carga la propiedad `Courses` del modelo de vista con las entidades `Course` de la propiedad de navegación `CourseAssignments` de ese instructor.

El método `Where` devuelve una colección. Sin embargo, en este caso, el filtro seleccionará una sola entidad, por lo que se llamará al método `Single` para convertir la colección en una sola entidad `Instructor`. La entidad `Instructor` proporciona acceso a la propiedad `CourseAssignments`. `CourseAssignments` proporciona acceso a las entidades `Course` relacionadas.

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

El método `Single` se usa en una colección cuando la colección tiene un solo elemento. El método `Single` inicia una excepción si la colección está vacía o hay más de un elemento. Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía.

El código siguiente rellena la propiedad `Enrollments` del modelo de vista cuando se selecciona un curso:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Actualizar la página de índice de instructores

Actualice *Pages/Instructors/Index.cshtml* con el código siguiente.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

En el código anterior se realizan los cambios siguientes:

* Se actualiza la directiva `page` de `@page` a `@page "{id:int?}"`. `"{id:int?}"` es una plantilla de ruta. La plantilla de ruta cambia las cadenas de consulta enteras de la dirección URL por datos de ruta. Por ejemplo, al hacer clic en el vínculo **Select** de un instructor con únicamente la directiva `@page`, se genera una dirección URL similar a la siguiente:

  `https://localhost:5001/Instructors?id=2`

  Cuando la directiva de página es `@page "{id:int?}"`, la dirección URL es:

  `https://localhost:5001/Instructors/2`

* Se agrega una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL. Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Se agrega una columna **Courses** en la que se muestran los cursos que imparte cada instructor. Para obtener más información sobre esta sintaxis de Razor, vea [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition).

* Se agrega código que agrega de forma dinámica `class="success"` al elemento `tr` del instructor y curso seleccionados. Esto establece el color de fondo de la fila seleccionada mediante una clase de arranque.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Se agrega un hipervínculo nuevo con la etiqueta **Select**. Este vínculo envía el identificador del instructor seleccionado al método `Index` y establece un color de fondo.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Se agrega una tabla de cursos para el instructor seleccionado.

* Se agrega una tabla de inscripciones de alumnos para el curso seleccionado.

Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la `Location` (oficina) de la entidad `OfficeAssignment` relacionada. Si `OfficeAssignment` es NULL, se muestra una celda de tabla vacía.

Haga clic en el vínculo **Select** de un instructor. Se muestran los cambios de estilo de fila y los cursos asignados a ese instructor.

Seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Uso de Single

Se puede pasar el método `Single` en la condición `Where` en lugar de llamar al método `Where` por separado:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

El uso de `Single` con una condición Where es una cuestión de preferencia personal. No proporciona ninguna ventaja sobre el uso del método `Where`.

## <a name="explicit-loading"></a>Carga explícita

En el código actual se especifica la carga diligente para `Enrollments` y `Students`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Imagine que los usuarios rara vez querrán ver las inscripciones en un curso. En ese caso, una optimización sería cargar solamente los datos de inscripción si se solicitan. En esta sección, se actualiza `OnGetAsync` para usar la carga explícita de `Enrollments` y `Students`.

Actualice *Pages/Instructors/Index.cshtml.cs* con el código siguiente.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

En el código anterior se quitan las llamadas al método *ThenInclude* para los datos de inscripción y estudiantes. Si se selecciona un curso, el código de carga explícita recupera lo siguiente:

* Las entidades `Enrollment` para el curso seleccionado.
* Las entidades `Student` para cada `Enrollment`.

Observe que en el código anterior `.AsNoTracking()` se convierte en comentario. Las propiedades de navegación solo se pueden cargar explícitamente para las entidades sometidas a seguimiento.

Pruebe la aplicación. Desde la perspectiva del usuario, la aplicación se comporta exactamente igual a la versión anterior.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial se muestra cómo actualizar datos relacionados.

>[!div class="step-by-step"]
>[Tutorial anterior](xref:data/ef-rp/complex-data-model)
>[Tutorial siguiente](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial, se leen y se muestran datos relacionados. Los datos relacionados son los que EF Core carga en las propiedades de navegación.

Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instrucciones de descarga](xref:index#how-to-download-a-sample).

En las ilustraciones siguientes se muestran las páginas completadas para este tutorial:

![Página de índice de cursos](read-related-data/_static/courses-index.png)

![Página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Carga diligente, explícita y diferida de datos relacionados

EF Core puede cargar datos relacionados en las propiedades de navegación de una entidad de varias maneras:

* [Carga diligente](/ef/core/querying/related-data#eager-loading). La carga diligente es cuando una consulta para un tipo de entidad también carga las entidades relacionadas. Cuando se lee la entidad, se recuperan sus datos relacionados. Esto normalmente da como resultado una única consulta de combinación en la que se recuperan todos los datos que se necesitan. EF Core emitirá varias consultas para algunos tipos de carga diligente. La emisión de varias consultas puede ser más eficaz de lo que eran algunas consultas de EF6 cuando había una sola consulta. La carga diligente se especifica con los métodos `Include` y `ThenInclude`.

  ![Ejemplo de carga diligente](read-related-data/_static/eager-loading.png)
 
  La carga diligente envía varias consultas cuando se incluye una propiedad de navegación de colección:

  * Una consulta para la consulta principal 
  * Una consulta para cada colección "perimetral" en el árbol de la carga.

* Separe las consultas con `Load`: los datos se pueden recuperar en distintas consultas y EF Core "corrige" las propiedades de navegación. "Corregir" significa que EF Core rellena automáticamente las propiedades de navegación. La separación de las consultas con `Load` es más parecido a la carga explícita que a la carga diligente.

  ![Ejemplo de consultas independientes](read-related-data/_static/separate-queries.png)

  Nota: EF Core corrige automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Incluso si los datos de una propiedad de navegación *no* se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.

* [Carga explícita](/ef/core/querying/related-data#explicit-loading). Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. Se debe escribir código para recuperar los datos relacionados cuando sea necesario. La carga explícita con consultas independientes da como resultado varias consultas que se envían a la base de datos. Con la carga explícita, el código especifica las propiedades de navegación que se van a cargar. Use el método `Load` para realizar la carga explícita. Por ejemplo:

  ![Ejemplo de carga explícita](read-related-data/_static/explicit-loading.png)

* [Carga diferida](/ef/core/querying/related-data#lazy-loading). [Se ha agregado la carga diferida a EF Core en la versión 2.1](/ef/core/querying/related-data#lazy-loading). Cuando la entidad se lee por primera vez, no se recuperan datos relacionados. La primera vez que se obtiene acceso a una propiedad de navegación, se recuperan automáticamente los datos necesarios para esa propiedad de navegación. Cada vez que se obtiene acceso a una propiedad de navegación, se envía una consulta a la base de datos.

* El operador `Select` solo carga los datos relacionados necesarios.

## <a name="create-a-course-page-that-displays-department-name"></a>Crear una página de cursos en la que se muestre el nombre de departamento

La entidad Course incluye una propiedad de navegación que contiene la entidad `Department`. La entidad `Department` contiene el departamento al que se asigna el curso.

Para mostrar el nombre del departamento asignado en una lista de cursos:

* Obtenga la propiedad `Name` desde la entidad `Department`.
* La entidad `Department` procede de la propiedad de navegación `Course.Department`.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Aplicar scaffolding al modelo de Course

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-the-student-model) y use `Course` para la clase de modelo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

El comando anterior aplica scaffolding al modelo `Course`. Abra el proyecto en Visual Studio.

Abra *Pages/Courses/Index.cshtml.cs* y examine el método `OnGetAsync`. El motor de scaffolding especificado realiza la carga diligente de la propiedad de navegación `Department`. El método `Include` especifica la carga diligente.

Ejecute la aplicación y haga clic en el vínculo **Courses**. En la columna Department se muestra el `DepartmentID`, lo que no resulta útil.

Actualice el método `OnGetAsync` con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

El código anterior agrega `AsNoTracking`. `AsNoTracking` mejora el rendimiento porque no se realiza el seguimiento de las entidades devueltas. No se realiza el seguimiento de las entidades porque no se actualizan en el contexto actual.

Actualice *Pages/Courses/Index.cshtml* con el marcado resaltado siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Se han realizado los cambios siguientes en el código con scaffolding:

* Ha cambiado el título de Index a Courses.
* Ha agregado una columna **Number** en la que se muestra el valor de propiedad `CourseID`. De forma predeterminada, las claves principales no tienen scaffolding porque normalmente no tienen sentido para los usuarios finales. Pero en este caso, la clave principal es significativa.
* Ha cambiado la columna **Department** para mostrar el nombre del departamento. El código muestra la propiedad `Name` de la entidad `Department` que se carga en la propiedad de navegación `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Ejecute la aplicación y haga clic en la pestaña **Courses** para ver la lista con los nombres de departamento.

![Página de índice de cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Carga de datos relacionados con Select

El método `OnGetAsync` carga los datos relacionados con el método `Include`:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

El operador `Select` solo carga los datos relacionados necesarios. Para elementos individuales, como el `Department.Name`, se usa INNER JOIN de SQL. En las colecciones, se usa otro acceso de base de datos, como también hace el operador `Include` en las colecciones.

En el código siguiente se cargan los datos relacionados con el método `Select`:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

El `CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Vea [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obtener un ejemplo completo.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Crear una página de instructores en la que se muestran los cursos y las inscripciones

En esta sección, se crea la página de instructores.

<a name="IP"></a>
![Página de índice de instructores](read-related-data/_static/instructors-index.png)

En esta página se leen y muestran los datos relacionados de las maneras siguientes:

* En la lista de instructores se muestran datos relacionados de la entidad `OfficeAssignment` (Office en la imagen anterior). Las entidades `Instructor` y `OfficeAssignment` se encuentran en una relación de uno a cero o uno. Para las entidades `OfficeAssignment` se usa la carga diligente. Normalmente la carga diligente es más eficaz cuando es necesario mostrar los datos relacionados. En este caso, se muestran las asignaciones de oficina para los instructores.
* Cuando el usuario selecciona un instructor (Harui en la imagen anterior), se muestran las entidades `Course` relacionadas. Las entidades `Instructor` y `Course` se encuentran en una relación de varios a varios. La carga diligente se usa con las entidades `Course` y sus entidades `Department` relacionadas. En este caso, es posible que las consultas independientes sean más eficaces porque solo se necesitan cursos para el instructor seleccionado. En este ejemplo se muestra cómo usar la carga diligente para propiedades de navegación en entidades que se encuentran en propiedades de navegación.
* Cuando el usuario selecciona un curso (Chemistry [Química] en la imagen anterior), se muestran los datos relacionados de la entidad `Enrollments`. En la imagen anterior, se muestra el nombre del alumno y la calificación. Las entidades `Course` y `Enrollment` se encuentran en una relación uno a varios.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Crear un modelo de vista para la vista de índice de instructores

En la página Instructors se muestran datos de tres tablas diferentes. Se crea un modelo de vista que incluye las tres entidades que representan las tres tablas.

En la carpeta *SchoolViewModels*, cree *InstructorIndexData.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Aplicar scaffolding al modelo de Instructor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-the-student-model) y use `Instructor` para la clase de modelo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

El comando anterior aplica scaffolding al modelo `Instructor`. 
Ejecute la aplicación y vaya a la página de instructores.

Reemplace *Pages/Instructors/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

El método `OnGetAsync` acepta datos de ruta opcionales para el identificador del instructor seleccionado.

Examine la consulta en el archivo *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

La consulta tiene dos instrucciones include:

* `OfficeAssignment`: se muestra en la [vista de instructores](#IP).
* `CourseAssignments`: muestra los cursos impartidos.

### <a name="update-the-instructors-index-page"></a>Actualizar la página de índice de instructores

Actualice *Pages/Instructors/Index.cshtml* con el marcado siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

En el marcado anterior se realizan los cambios siguientes:

* Se actualiza la directiva `page` de `@page` a `@page "{id:int?}"`. `"{id:int?}"` es una plantilla de ruta. La plantilla de ruta cambia las cadenas de consulta enteras de la dirección URL por datos de ruta. Por ejemplo, al hacer clic en el vínculo **Select** de un instructor con únicamente la directiva `@page`, se genera una dirección URL similar a la siguiente:

  `http://localhost:1234/Instructors?id=2`

  Cuando la directiva de página es `@page "{id:int?}"`, la dirección URL anterior es:

  `http://localhost:1234/Instructors/2`

* El título de página es **Instructors**.
* Se ha agregado una columna **Office** en la que se muestra `item.OfficeAssignment.Location` solo si `item.OfficeAssignment` no es NULL. Dado que se trata de una relación de uno a cero o uno, es posible que no haya una entidad OfficeAssignment relacionada.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Se ha agregado una columna **Courses** en la que se muestran los cursos que imparte cada instructor. Para obtener más información sobre esta sintaxis de Razor, vea [Transición de línea explícita](xref:mvc/views/razor#explicit-line-transition).

* Ha agregado código que agrega dinámicamente `class="success"` al elemento `tr` del instructor seleccionado. Esto establece el color de fondo de la fila seleccionada mediante una clase de arranque.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Se ha agregado un hipervínculo nuevo con la etiqueta **Select**. Este vínculo envía el identificador del instructor seleccionado al método `Index` y establece un color de fondo.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Ejecute la aplicación y haga clic en la pestaña **Instructors**. En la página se muestra la `Location` (oficina) de la entidad `OfficeAssignment` relacionada. Si OfficeAssignment es NULL, se muestra una celda de tabla vacía.

Haga clic en el vínculo **Select**. El estilo de la fila cambia.

### <a name="add-courses-taught-by-selected-instructor"></a>Agregar cursos impartidos por el instructor seleccionado

Actualice el método `OnGetAsync` de *Pages/Instructors/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Agregue `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Examine la consulta actualizada:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

En la consulta anterior se agregan las entidades `Department`.

El código siguiente se ejecuta cuando se selecciona un instructor (`id != null`). El instructor seleccionado se recupera de la lista de instructores del modelo de vista. Se carga la propiedad `Courses` del modelo de vista con las entidades `Course` de la propiedad de navegación `CourseAssignments` de ese instructor.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

El método `Where` devuelve una colección. En el método `Where` anterior, solo se devuelve una entidad `Instructor`. El método `Single` convierte la colección en una sola entidad `Instructor`. La entidad `Instructor` proporciona acceso a la propiedad `CourseAssignments`. `CourseAssignments` proporciona acceso a las entidades `Course` relacionadas.

![Relación de varios a varios Instructor-to-Courses](complex-data-model/_static/courseassignment.png)

El método `Single` se usa en una colección cuando la colección tiene un solo elemento. El método `Single` inicia una excepción si la colección está vacía o hay más de un elemento. Una alternativa es `SingleOrDefault`, que devuelve una valor predeterminado (NULL, en este caso) si la colección está vacía. El uso de `SingleOrDefault` en una colección vacía:

* Inicia una excepción (al tratar de buscar una propiedad `Courses` en una referencia nula).
* El mensaje de excepción indicará con menos claridad la causa del problema.

El código siguiente rellena la propiedad `Enrollments` del modelo de vista cuando se selecciona un curso:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Agregue el siguiente marcado al final de la página de Razor *Pages/Instructors/Index.cshtml*:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

En el marcado anterior se muestra una lista de cursos relacionados con un instructor cuando se selecciona un instructor.

Pruebe la aplicación. Haga clic en un vínculo **Select** en la página de instructores.

### <a name="show-student-data"></a>Mostrar datos de estudiante

En esta sección, la aplicación se actualiza para mostrar los datos de estudiante para un curso seleccionado.

Actualice la consulta en el método `OnGetAsync` de *Pages/Instructors/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Actualice *Pages/Instructors/Index.cshtml*. Agregue el marcado siguiente al final del archivo:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

En el marcado anterior se muestra una lista de los estudiantes que están inscritos en el curso seleccionado.

Actualice la página y seleccione un instructor. Seleccione un curso para ver la lista de los estudiantes inscritos y sus calificaciones.

![Instructor y curso seleccionados en la página de índice de instructores](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Uso de Single

Se puede pasar el método `Single` en la condición `Where` en lugar de llamar al método `Where` por separado:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

El enfoque de `Single` anterior no ofrece ninguna ventaja con respecto a `Where`. Algunos desarrolladores prefieren el estilo del enfoque de `Single`.

## <a name="explicit-loading"></a>Carga explícita

En el código actual se especifica la carga diligente para `Enrollments` y `Students`:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Imagine que los usuarios rara vez querrán ver las inscripciones en un curso. En ese caso, una optimización sería cargar solamente los datos de inscripción si se solicitan. En esta sección, se actualiza `OnGetAsync` para usar la carga explícita de `Enrollments` y `Students`.

Actualice `OnGetAsync` con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

En el código anterior se quitan las llamadas al método *ThenInclude* para los datos de inscripción y estudiantes. Si se selecciona un curso, el código resaltado recupera lo siguiente:

* Las entidades `Enrollment` para el curso seleccionado.
* Las entidades `Student` para cada `Enrollment`.

Tenga en cuenta que en el código anterior `.AsNoTracking()` se convierte en comentario. Las propiedades de navegación solo se pueden cargar explícitamente para las entidades sometidas a seguimiento.

Pruebe la aplicación. Desde la perspectiva de los usuarios, la aplicación se comporta exactamente igual a la versión anterior.

En el siguiente tutorial se muestra cómo actualizar datos relacionados.

## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial (parte 1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Versión en YouTube de este tutorial (parte 2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Anterior](xref:data/ef-rp/complex-data-model)
>[Siguiente](xref:data/ef-rp/update-related-data)

::: moniker-end
