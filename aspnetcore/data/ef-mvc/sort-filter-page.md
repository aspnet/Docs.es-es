---
title: 'Tutorial: Adición de ordenación, filtrado y paginación: ASP.NET MVC con EF Core'
description: En este tutorial agregaremos la funcionalidad de ordenación, filtrado y paginación a la página de índice de Students. También crearemos una página que realice agrupaciones sencillas.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 8e425d413471912c763c4892a90e9d12039efec4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053987"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Tutorial: Adición de ordenación, filtrado y paginación: ASP.NET MVC con EF Core

En el tutorial anterior, implementamos un conjunto de páginas web para operaciones básicas de CRUD para las entidades Student. En este tutorial agregaremos la funcionalidad de ordenación, filtrado y paginación a la página de índice de Students. También crearemos una página que realice agrupaciones sencillas.

En la siguiente ilustración se muestra el aspecto que tendrá la página cuando haya terminado. Los encabezados de columna son vínculos en los que el usuario puede hacer clic para ordenar las columnas correspondientes. Si se hace clic de forma consecutiva en el encabezado de una columna, el criterio de ordenación alterna entre ascendente y descendente.

![Página de índice de Students](sort-filter-page/_static/paging.png)

En este tutorial ha:

> [!div class="checklist"]
> * Agrega vínculos de ordenación de columnas
> * Agrega un cuadro de búsqueda
> * Agrega paginación al índice de Students
> * Agrega paginación al método Index
> * Agrega vínculos de paginación
> * Crea una página About

## <a name="prerequisites"></a>Requisitos previos

* [Implementación de la funcionalidad CRUD](crud.md)

## <a name="add-column-sort-links"></a>Agrega vínculos de ordenación de columnas

Para agregar ordenación a la página de índice de Student, deberá cambiar el método `Index` del controlador de Students y agregar código a la vista de índice de Student.

### <a name="add-sorting-functionality-to-the-index-method"></a>Agregar la funcionalidad de ordenación al método Index

En *StudentsController.cs*, reemplace el método `Index` por el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Este código recibe un parámetro `sortOrder` de la cadena de consulta en la dirección URL. ASP.NET Core MVC proporciona el valor de la cadena de consulta como un parámetro al método de acción. El parámetro es una cadena que puede ser "Name" o "Date", seguido (opcionalmente) por un guión bajo y la cadena "desc" para especificar el orden descendente. El criterio de ordenación predeterminado es el ascendente.

La primera vez que se solicita la página de índice, no hay ninguna cadena de consulta. Los alumnos se muestran por apellido en orden ascendente, que es el valor predeterminado establecido por el caso desestimado en la instrucción `switch`. Cuando el usuario hace clic en un hipervínculo de encabezado de columna, se proporciona el valor `sortOrder` correspondiente en la cadena de consulta.

La vista usa los dos elementos `ViewData` (NameSortParm y DateSortParm) para configurar los hipervínculos del encabezado de columna con los valores de cadena de consulta adecuados.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Estas son las instrucciones ternarias. La primera de ellas especifica que, si el parámetro `sortOrder` es NULL o está vacío, NameSortParm debe establecerse en "name_desc"; en caso contrario, se debe establecer en una cadena vacía. Estas dos instrucciones habilitan la vista para establecer los hipervínculos de encabezado de columna de la forma siguiente:

|  Criterio de ordenación actual  | Hipervínculo de apellido | Hipervínculo de fecha |
|:--------------------:|:-------------------:|:--------------:|
| Apellido: ascendente  | descending          | ascending      |
| Apellido: descendente | ascending           | ascending      |
| Fecha: ascendente       | ascending           | descending     |
| Fecha: descendente      | ascending           | ascending      |

El método usa LINQ to Entities para especificar la columna por la que se va a ordenar. El código crea una variable `IQueryable` antes de la instrucción de cambio, la modifica en la instrucción de cambio y llama al método `ToListAsync` después de la instrucción `switch`. Al crear y modificar variables `IQueryable`, no se envía ninguna consulta a la base de datos. La consulta no se ejecuta hasta que convierta el objeto `IQueryable` en una colección mediante una llamada a un método, como `ToListAsync`. Por lo tanto, este código produce una única consulta que no se ejecuta hasta la instrucción `return View`.

Este código podría detallarse con un gran número de columnas. [En el último tutorial de esta serie](advanced.md#dynamic-linq) se muestra cómo escribir código que le permita pasar el nombre de la columna `OrderBy` en una variable de cadenas.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Agregar hipervínculos del encabezado de columna a la vista de índice de Student

Reemplace el código de *Views/Students/Index.cshtml* por el código siguiente para agregar los hipervínculos del encabezado de columna. Se resaltan las líneas modificadas.

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Este código usa la información que se incluye en las propiedades `ViewData` para configurar hipervínculos con los valores de cadena de consulta adecuados.

Ejecute la aplicación, seleccione la ficha **Students** y haga clic en los encabezados de columna **Last Name** y **Enrollment Date** para comprobar que la ordenación funciona correctamente.

![Página de índice de Students ordenada por nombre](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Agrega un cuadro de búsqueda

Para agregar filtrado a la página de índice de Students, agregue un cuadro de texto y un botón de envío a la vista y haga los cambios correspondientes en el método `Index`. El cuadro de texto le permite escribir la cadena que quiera buscar en los campos de nombre y apellido.

### <a name="add-filtering-functionality-to-the-index-method"></a>Agregar la funcionalidad de filtrado al método Index

En *StudentsController.cs*, reemplace el método `Index` por el código siguiente (los cambios se resaltan).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Ha agregado un parámetro `searchString` al método `Index`. El valor de la cadena de búsqueda se recibe desde un cuadro de texto que agregará a la vista de índice. También ha agregado a la instrucción LINQ una cláusula where que solo selecciona los alumnos cuyo nombre o apellido contienen la cadena de búsqueda. La instrucción que agrega la cláusula where solo se ejecuta si hay un valor que se tiene que buscar.

> [!NOTE]
> Aquí se llama al método `Where` en un objeto `IQueryable` y el filtro se procesa en el servidor. En algunos escenarios, puede hacer una llamada al método `Where` como un método de extensión en una colección en memoria. (Por ejemplo, imagine que cambia la referencia a `_context.Students`, de modo que, en lugar de hacer referencia a EF `DbSet`, haga referencia a un método de repositorio que devuelva una colección `IEnumerable`). Lo más habitual es que el resultado fuera el mismo, pero en algunos casos puede ser diferente.
>
>Por ejemplo, la implementación de .NET Framework del método `Contains` realiza de forma predeterminada una comparación que diferencia entre mayúsculas y minúsculas, pero en SQL Server se determina por la configuración de intercalación de la instancia de SQL Server. De forma predeterminada, esta opción de configuración no diferencia entre mayúsculas y minúsculas. Podría llamar al método `ToUpper` para hacer explícitamente que la prueba no distinga entre mayúsculas y minúsculas:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* . Esto garantiza que los resultados permanezcan invariables aunque cambie el código más adelante para usar un repositorio que devuelva una colección `IEnumerable` en vez de un objeto `IQueryable`. (Al hacer una llamada al método `Contains` en una colección `IEnumerable`, obtendrá la implementación de .NET Framework; al hacer una llamada a un objeto `IQueryable`, obtendrá la implementación del proveedor de base de datos). En cambio, el rendimiento de esta solución se ve reducido. El código `ToUpper` tendría que poner una función en la cláusula WHERE de la instrucción SELECT de TSQL. Esto impediría que el optimizador usara un índice. Dado que principalmente SQL se instala de forma que no diferencia entre mayúsculas y minúsculas, es mejor que evite el código `ToUpper` hasta que migre a un almacén de datos que distinga entre mayúsculas y minúsculas.

### <a name="add-a-search-box-to-the-student-index-view"></a>Agregar un cuadro de búsqueda a la vista de índice de Student

En *Views/Student/Index.cshtml*, agregue el código resaltado justo antes de la etiqueta de apertura de tabla para crear un título, un cuadro de texto y un botón de **búsqueda**.

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Este código usa el [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) `<form>` para agregar el cuadro de texto de búsqueda y el botón. De forma predeterminada, el asistente de etiquetas `<form>` envía datos de formulario con POST, lo que significa que los parámetros se pasan en el cuerpo del mensaje HTTP y no en la dirección URL como cadenas de consulta. Al especificar HTTP GET, los datos de formulario se pasan en la dirección URL como cadenas de consulta, lo que permite que los usuarios marquen la dirección URL. Las directrices de W3C recomiendan que use GET cuando la acción no produzca ninguna actualización.

Ejecute la aplicación, seleccione la ficha **Students**, escriba una cadena de búsqueda y haga clic en Search para comprobar que el filtrado funciona correctamente.

![Página de índice de Students con filtrado](sort-filter-page/_static/filtering.png)

Fíjese en que la dirección URL contiene la cadena de búsqueda.

```html
http://localhost:5813/Students?SearchString=an
```

Si marca esta página, obtendrá la lista filtrada al usar el marcador. El hecho de agregar `method="get"` a la etiqueta `form` es lo que ha provocado que se generara la cadena de consulta.

En esta fase, si hace clic en un vínculo de ordenación del encabezado de columna, el valor de filtro que especificó en el cuadro de **búsqueda** se perderá. Podrá corregirlo en la siguiente sección.

## <a name="add-paging-to-students-index"></a>Agrega paginación al índice de Students

Para agregar paginación a la página de índice de Students, tendrá que crear una clase `PaginatedList` que use las instrucciones `Skip` y `Take` para filtrar los datos en el servidor en lugar de recuperar siempre todas las filas de la tabla. A continuación, podrá realizar cambios adicionales en el método `Index` y agregar botones de paginación a la vista `Index`. La ilustración siguiente muestra los botones de paginación.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

En la carpeta del proyecto, cree `PaginatedList.cs` y después reemplace el código de plantilla por el código siguiente.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

El método `CreateAsync` en este código toma el tamaño y el número de la página y aplica las instrucciones `Skip` y `Take` correspondientes a `IQueryable`. Cuando se llama a `ToListAsync` en `IQueryable`, devuelve una lista que solo contiene la página solicitada. Las propiedades `HasPreviousPage` y `HasNextPage` se pueden usar para habilitar o deshabilitar los botones de página **Previous** y **Next**.

Para crear el objeto `PaginatedList<T>`, se usa un método `CreateAsync` en vez de un constructor, porque los constructores no pueden ejecutar código asincrónico.

## <a name="add-paging-to-index-method"></a>Agrega paginación al método Index

En *StudentsController.cs*, reemplace el método `Index` por el código siguiente.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Este código agrega un parámetro de número de página, un parámetro de criterio de ordenación actual y un parámetro de filtro actual a la firma del método.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

La primera vez que se muestra la página, o si el usuario no ha hecho clic en un vínculo de ordenación o paginación, todos los parámetros son nulos.  Si se hace clic en un vínculo de paginación, la variable de página contiene el número de página que se tiene que mostrar.

El elemento `ViewData`, denominado CurrentSort, proporciona la vista con el criterio de ordenación actual, que debe incluirse en los vínculos de paginación para mantener el criterio de ordenación durante la paginación.

El elemento `ViewData`, denominado CurrentFilter, proporciona la vista con la cadena de filtro actual. Este valor debe incluirse en los vínculos de paginación para mantener la configuración de filtrado durante la paginación y debe restaurarse en el cuadro de texto cuando se vuelve a mostrar la página.

Si se cambia la cadena de búsqueda durante la paginación, la página debe restablecerse a 1, porque el nuevo filtro puede hacer que se muestren diferentes datos. La cadena de búsqueda cambia cuando se escribe un valor en el cuadro de texto y se presiona el botón Submit. En ese caso, el parámetro `searchString` no es NULL.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

Al final del método `Index`, el método `PaginatedList.CreateAsync` convierte la consulta del alumno en una sola página de alumnos de un tipo de colección que admita la paginación. Entonces, esa única página de alumnos pasa a la vista.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

El método `PaginatedList.CreateAsync` toma un número de página. Los dos signos de interrogación representan el operador de uso combinado de NULL. El operador de uso combinado de NULL define un valor predeterminado para un tipo que acepta valores NULL; la expresión `(pageNumber ?? 1)` devuelve el valor de `pageNumber` si tiene algún valor o devuelve 1 si `pageNumber` es NULL.

## <a name="add-paging-links"></a>Agrega vínculos de paginación

En *Views/Students/Index.cshtml*, reemplace el código existente por el código siguiente. Los cambios aparecen resaltados.

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

La instrucción `@model` de la parte superior de la página especifica que ahora la vista obtiene un objeto `PaginatedList<T>` en lugar de un objeto `List<T>`.

Los vínculos del encabezado de la columna usan la cadena de consulta para pasar la cadena de búsqueda actual al controlador, de modo que el usuario pueda ordenar los resultados del filtro:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Los botones de paginación se muestran mediante asistentes de etiquetas:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Ejecute la aplicación y vaya a la página Students.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

Haga clic en los vínculos de paginación en distintos criterios de ordenación para comprobar que la paginación funciona correctamente. A continuación, escriba una cadena de búsqueda e intente llevar a cabo la paginación de nuevo, para comprobar que la paginación también funciona correctamente con filtrado y ordenación.

## <a name="create-an-about-page"></a>Crea una página About

En la página **About** del sitio web de Contoso University, se muestran cuántos alumnos se han inscrito en cada fecha de inscripción. Esto requiere realizar agrupaciones y cálculos sencillos en los grupos. Para conseguirlo, haga lo siguiente:

* Cree una clase de modelo de vista para los datos que necesita pasar a la vista.
* Cree el método About en el controlador Home.
* Cree la vista About.

### <a name="create-the-view-model"></a>Creación del modelo de vista

Cree una carpeta *SchoolViewModels* en la carpeta *Models*.

En la nueva carpeta, agregue un archivo de clase *EnrollmentDateGroup.cs* y reemplace el código de plantilla con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Modificación del controlador Home

En *HomeController.cs*, agregue lo siguiente mediante instrucciones en la parte superior del archivo:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Agregue una variable de clase para el contexto de base de datos inmediatamente después de la llave de apertura para la clase y obtenga una instancia del contexto de ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Agregue un método `About` en el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

La instrucción LINQ agrupa las entidades de alumnos por fecha de inscripción, calcula la cantidad de entidades que se incluyen en cada grupo y almacena los resultados en una colección de objetos de modelo de la vista `EnrollmentDateGroup`.

### <a name="create-the-about-view"></a>Creación de la vista About

Agregue un archivo *Views/Home/About.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

Ejecute la aplicación y vaya a la página About. En una tabla se muestra el número de alumnos para cada fecha de inscripción.

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Agregado vínculos de ordenación de columnas
> * Agregado un cuadro de búsqueda
> * Agregado paginación al índice de Students
> * Agregado paginación al método Index
> * Agregado vínculos de paginación
> * Creado una página About

Pase al tutorial siguiente para obtener información sobre cómo controlar los cambios en el modelo de datos mediante migraciones.

> [!div class="nextstepaction"]
> [Siguiente: Control de los cambios en el modelo de datos](migrations.md)
