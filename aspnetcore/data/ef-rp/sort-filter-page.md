---
title: 'Página 3. Razor Pages con EF Core en ASP.NET Core: Ordenación, filtrado y paginación'
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 51a1e2a90259898262ac655b7a0e8a55d766f0c7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061046"
---
# <a name="part-3-no-locrazor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a>Página 3. Razor Pages con EF Core en ASP.NET Core: Ordenación, filtrado y paginación

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

En este tutorial se agrega funcionalidad de ordenación, filtrado y paginación a las páginas Student.

En la siguiente ilustración se muestra una página completa. Los encabezados de columna son vínculos interactivos para ordenar la columna. Haga clic de forma consecutiva en el encabezado de una columna para alternar el criterio de ordenación entre ascendente y descendente.

![Página de índice de Students](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Adición de ordenación

Reemplace el código de *Pages/Students/Index.cshtml.cs* por el código siguiente para agregar ordenación.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All)]

El código anterior:

* Requiere que se agregue `using System;`.
* Agrega propiedades que contienen los parámetros de ordenación.
* Cambia el nombre de la propiedad `Student` a `Students`.
* Reemplaza el código del método `OnGetAsync`.

El método `OnGetAsync` recibe un parámetro `sortOrder` de la cadena de consulta en la dirección URL. El [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) genera la dirección URL y la cadena de consulta.

El valor del parámetro `sortOrder` es `Name` o `Date`. Opcionalmente, el parámetro `sortOrder` puede ir seguido de `_desc` para especificar el orden descendente. El criterio de ordenación predeterminado es el ascendente.

Cuando se solicita la página de índice del vínculo **Students** no hay ninguna cadena de consulta. Los alumnos se muestran en orden ascendente por apellido. El orden ascendente por apellido es `default` en la instrucción `switch`. Cuando el usuario hace clic en un vínculo de encabezado de columna, se proporciona el valor `sortOrder` correspondiente en el valor de la cadena de consulta.

La instancia de Razor Pages usa `NameSort` y `DateSort` para configurar los hipervínculos del encabezado de columna con los valores de cadena de consulta adecuados:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

En el código se usa el [operador condicional ?:](/dotnet/csharp/language-reference/operators/conditional-operator) de C#. El operador `?:` es ternario, por lo que toma tres operandos. La primera línea especifica que, cuando `sortOrder` es NULL o está vacío, `NameSort` se establece en `name_desc`. Si `sortOrder` * *_no_* es NULL ni está vacío, `NameSort` se establece en una cadena vacía.

Estas dos instrucciones habilitan la página para establecer los hipervínculos de encabezado de columna de la siguiente forma:

| Criterio de ordenación actual   | Hipervínculo de apellido | Hipervínculo de fecha |
|:--------------------:|:-------------------:|:--------------:|
| Apellido: ascendente  | descending          | ascending      |
| Apellido: descendente | ascending           | ascending      |
| Fecha: ascendente       | ascending           | descending     |
| Fecha: descendente      | ascending           | ascending      |

El método usa LINQ to Entities para especificar la columna por la que se va a ordenar. El código inicializa un `IQueryable<Student>` antes de la instrucción switch y lo modifica en la instrucción switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Cuando se crea o se modifica `IQueryable`, no se envía ninguna consulta a la base de datos. La consulta no se ejecuta hasta que el objeto `IQueryable` se convierte en una colección. `IQueryable` se convierte en una colección mediante una llamada a un método como `ToListAsync`. Por lo tanto, el código `IQueryable` produce una única consulta que no se ejecuta hasta la siguiente instrucción:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` se podría detallar con un gran número de columnas ordenables. Para obtener información sobre una forma alternativa de codificar esta funcionalidad, vea [Usar LINQ dinámico para simplificar el código](xref:data/ef-mvc/advanced#dynamic-linq) en la versión para MVC de esta serie de tutoriales.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Agregar hipervínculos de encabezado de columna a la página de índice de Student

Reemplace el código de _Students/Index.cshtml* por el código siguiente. Los cambios aparecen resaltados.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

El código anterior:

* Agrega hipervínculos a los encabezados de columna `LastName` y `EnrollmentDate`.
* Usa la información de `NameSort` y `DateSort` para configurar hipervínculos con los valores de criterio de ordenación actuales.
* Cambia el encabezado de la página de Index a Students.
* Cambia `Model.Student` por `Model.Students`.

Para comprobar que la ordenación funciona:

* Ejecute la aplicación y haga clic en la pestaña **Students**.
* Haga clic en los encabezados de columna.

## <a name="add-filtering"></a>Adición de filtrado

Para agregar un filtro a la página de índice de Students:

* Se agregan un cuadro de texto y un botón de envío a la página de Razor. El cuadro de texto proporciona una cadena de búsqueda de nombre o apellido.
* El modelo de página se actualiza para usar el valor del cuadro de texto.

### <a name="update-the-ongetasync-method"></a>Actualización del método OnGetAsync

Reemplace el código de *Students/Index.cshtml.cs* por el código siguiente para agregar filtrado:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=17,22,26-30)]

El código anterior:

* Agrega el parámetro `searchString` al método `OnGetAsync` y guarda el valor del parámetro en la propiedad `CurrentFilter`. El valor de la cadena de búsqueda se recibe desde un cuadro de texto que se agrega en la siguiente sección.
* Agrega una cláusula `Where` a la instrucción LINQ. La cláusula `Where` selecciona solo los alumnos cuyo nombre o apellido contienen la cadena de búsqueda. La instrucción LINQ se ejecuta solo si hay un valor para buscar.

### <a name="iqueryable-vs-ienumerable"></a>Diferencias entre IQueryable e IEnumerable

El código llama al método <xref:System.Linq.Queryable.Where%2A> en un objeto `IQueryable` y el filtro se procesa en el servidor. En algunos escenarios, la aplicación puede hacer una llamada al método `Where` como un método de extensión en una colección en memoria. Por ejemplo, suponga que `_context.Students` cambia de `DbSet` de EF Core a un método de repositorio que devuelve una colección `IEnumerable`. Lo más habitual es que el resultado fuera el mismo, pero en algunos casos puede ser diferente.

Por ejemplo, la implementación de .NET Framework de `Contains` realiza una comparación que distingue mayúsculas de minúsculas de forma predeterminada. En SQL Server, la distinción entre mayúsculas y minúsculas de `Contains` viene determinada por la configuración de intercalación de la instancia de SQL Server. SQL Server no diferencia entre mayúsculas y minúsculas de forma predeterminada. De forma predeterminada, SQLite distingue mayúsculas de minúsculas. Se podría llamar a `ToUpper` para hacer explícitamente que la prueba no distinga entre mayúsculas y minúsculas:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

El código anterior se aseguraría de que el filtro no distinga entre mayúsculas y minúsculas incluso si se llama al método `Where` en una interfaz `IEnumerable` o se ejecuta en SQLite.

Cuando se llama a `Contains` en una colección `IEnumerable`, se usa la implementación de .NET Core. Cuando se llama a `Contains` en un objeto `IQueryable`, se usa la implementación de la base de datos.

La llamada a `Contains` en una instancia de `IQueryable`suele ser preferible por motivos de rendimiento. Con `IQueryable`, el filtrado lo realiza el servidor de base de datos. Si primero se crea `IEnumerable`, todas las filas tendrán que devolverse desde el servidor de base de datos.

Hay una disminución del rendimiento por llamar a `ToUpper`. El código `ToUpper` agrega una función en la cláusula WHERE de la instrucción SELECT de TSQL. La función agregada impide que el optimizador use un índice. Dado que SQL está instalado para no distinguir entre mayúsculas y minúsculas, es mejor evitar llamar a `ToUpper` cuando no sea necesario.

Para obtener más información, vea este artículo, en el que se describen [los procedimientos para usar consultas que no distinguen mayúsculas de minúsculas con el proveedor de SQLite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-no-locrazor-page"></a>Actualización de la página de Razor

Reemplace el código de *Pages/Student/Index.cshtml* para agregar un botón **Search**.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

El código anterior usa el [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) `<form>` para agregar el cuadro de texto de búsqueda y el botón. De forma predeterminada, el asistente de etiquetas `<form>` envía datos de formulario con POST. Con POST, los parámetros se pasan en el cuerpo del mensaje HTTP y no en la dirección URL. Cuando se usa el método HTTP GET, los datos del formulario se pasan en la dirección URL como cadenas de consulta. Pasar los datos con cadenas de consulta permite a los usuarios marcar la dirección URL. Las [directrices de W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recomiendan el uso de GET cuando la acción no produzca ninguna actualización.

Pruebe la aplicación:

* Seleccione la pestaña **Students** y escriba una cadena de búsqueda. Si usa SQLite, el filtro no distingue entre mayúsculas y minúsculas solo si ha implementado el código `ToUpper` opcional que se ha mostrado antes.

* Seleccione **Search**.

Fíjese en que la dirección URL contiene la cadena de búsqueda. Por ejemplo:

```browser-address-bar
https://localhost:5001/Students?SearchString=an
```

Si se colocó un marcador en la página, el marcador contiene la dirección URL a la página y la cadena de consulta de `SearchString`. El `method="get"` en la etiqueta `form` es lo que ha provocado que se generara la cadena de consulta.

Actualmente, cuando se selecciona un vínculo de ordenación del encabezado de columna, el filtro de valor del cuadro **Search** se pierde. El valor de filtro perdido se fija en la sección siguiente.

## <a name="add-paging"></a>Adición de paginación

En esta sección, se crea una clase `PaginatedList` para admitir la paginación. La clase `PaginatedList` usa las instrucciones `Skip` y `Take` para filtrar los datos en el servidor en lugar de recuperar todas las filas de la tabla. La ilustración siguiente muestra los botones de paginación.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Creación de la clase PaginatedList

En la carpeta del proyecto, cree `PaginatedList.cs` con el código siguiente:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

El método `CreateAsync` en el código anterior toma el tamaño y el número de la página, y aplica las instrucciones `Skip` y `Take` correspondientes a `IQueryable`. Cuando `ToListAsync` se llama en `IQueryable`, devuelve una lista que solo contiene la página solicitada. Las propiedades `HasPreviousPage` y `HasNextPage` se usan para habilitar o deshabilitar los botones de página **Previous** y **Next**.

El método `CreateAsync` se usa para crear la `PaginatedList<T>`. Un constructor no puede crear el objeto `PaginatedList<T>`; los constructores no pueden ejecutar código asincrónico.

### <a name="add-paging-to-the-pagemodel-class"></a>Adición de paginación a la clase PageModel

Reemplace el código de *Students/Index.cshtml.cs* para agregar paginación.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=15-20,23-30,57-59)]

El código anterior:

* Cambia el tipo de la propiedad `Students` de `IList<Student>` a `PaginatedList<Student>`.
* Agrega el índice de la página, el elemento `sortOrder` actual y el elemento `currentFilter` a la firma del método `OnGetAsync`.
* Guarda el criterio de ordenación en la propiedad `CurrentSort`.
* Restablece el índice de la página en 1 cuando hay una cadena de búsqueda nueva.
* Usa la clase `PaginatedList` para obtener las entidades Student.
* Establece `pageSize` en 3. Una aplicación real usaría [Configuración](xref:fundamentals/configuration/index) para establecer el valor de tamaño de página.

Todos los parámetros que recibe `OnGetAsync` son NULL cuando:

* Se llama a la página desde el vínculo **Students**.
* El usuario no ha seleccionado un vínculo de ordenación o paginación.

Cuando se hace clic en un vínculo de paginación, la variable de índice de página contiene el número de página que se tiene que mostrar.

La propiedad `CurrentSort` proporciona a la instancia de Razor Pages el criterio de ordenación actual. Se debe incluir el criterio de ordenación actual en los vínculos de paginación para mantener el criterio de ordenación durante la paginación.

La propiedad `CurrentFilter` proporciona a la instancia de Razor Pages la cadena de filtro actual. El valor `CurrentFilter`:

* Debe incluirse en los vínculos de paginación para mantener la configuración del filtro durante la paginación.
* Debe restaurarse en el cuadro de texto cuando se vuelva a mostrar la página.

Si se cambia la cadena de búsqueda durante la paginación, la página se restablece a 1. La página debe restablecerse a 1 porque el nuevo filtro puede hacer que se muestren diferentes datos. Cuando se escribe un valor de búsqueda y se selecciona **Submit** :

  * La cadena de búsqueda cambia.
  * El parámetro `searchString` no es NULL.

  El método `PaginatedList.CreateAsync` convierte la consulta del alumno en una sola página de alumnos de un tipo de colección que admita la paginación. Esa única página de alumnos se pasa a la página de Razor.

  Los dos signos de interrogación después de `pageIndex` en la llamada a `PaginatedList.CreateAsync` representan el [operador de uso combinado de NULL](/dotnet/csharp/language-reference/operators/null-conditional-operator). El operador de uso combinado de NULL define un valor predeterminado para un tipo que acepta valores NULL. La expresión `pageIndex ?? 1` devuelve el valor de `pageIndex` si tiene un valor; de lo contrario, devuelve 1.

### <a name="add-paging-links-to-the-no-locrazor-page"></a>Adición de vínculos de paginación a la instancia de Razor Pages

Reemplace el código de *Students/Index.cshtml* con el código siguiente. Se resaltan los cambios:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

En los vínculos de encabezado de columna se usa la cadena de consulta para pasar la cadena de búsqueda actual al método `OnGetAsync`:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Los botones de paginación se muestran mediante asistentes de etiquetas:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Ejecute la aplicación y vaya a la página Students.

* Para comprobar que la paginación funciona correctamente, haga clic en los vínculos de paginación en distintos criterios de ordenación.
* Para comprobar que la paginación también funciona correctamente con filtrado y ordenación, escriba una cadena de búsqueda e intente llevar a cabo la paginación de nuevo.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Adición de agrupación

En esta sección se crea una página About (Acerca de) en la que se muestran cuántos alumnos se han inscrito para cada fecha de inscripción. La actualización usa la agrupación e incluye los siguientes pasos:

* Cree un modelo de vista para los datos que se usan en la página **About**.
* Actualice la página About para usar el modelo de vista.

### <a name="create-the-view-model"></a>Creación del modelo de vista

Cree una carpeta *Models/SchoolViewModels*.

Cree *SchoolViewModels/EnrollmentDateGroup.cs* con el código siguiente:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-no-locrazor-page"></a>Creación de la instancia de Razor Pages

Cree un archivo *Pages/About.cshtml* con el código siguiente:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Creación del modelo de página

Actualice el archivo *Pages/About.cshtml.cs* con el código siguiente:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

La instrucción LINQ agrupa las entidades de alumnos por fecha de inscripción, calcula la cantidad de entidades que se incluyen en cada grupo y almacena los resultados en una colección de objetos de modelo de la vista `EnrollmentDateGroup`.

Ejecute la aplicación y vaya a la página About. En una tabla se muestra el número de alumnos para cada fecha de inscripción.

![Página About](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Pasos siguientes

En el tutorial siguiente, la aplicación usa las migraciones para actualizar el modelo de datos.

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/crud)
> [Tutorial siguiente](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial se agregan las funcionalidades de ordenación, filtrado, agrupación y paginación.

En la siguiente ilustración se muestra una página completa. Los encabezados de columna son vínculos interactivos para ordenar la columna. Si se hace clic de forma consecutiva en el encabezado de una columna, el criterio de ordenación cambia entre ascendente y descendente.

![Página de índice de Students](sort-filter-page/_static/paging.png)

Si experimenta problemas que no puede resolver, descargue la [aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Agregar ordenación a la página de índice

Agregue cadenas al elemento `PageModel` de *Students/Index.cshtml.cs* para que contenga los parámetros de ordenación:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Actualice el elemento `OnGetAsync` de *Students/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

El código anterior recibe un parámetro `sortOrder` de la cadena de consulta en la dirección URL. El [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
) genera la dirección URL (incluida la cadena de consulta).

El parámetro `sortOrder` es "Name" o "Date". Opcionalmente, el parámetro `sortOrder` puede ir seguido de "_desc" para especificar el orden descendente. El criterio de ordenación predeterminado es el ascendente.

Cuando se solicita la página de índice del vínculo **Students** no hay ninguna cadena de consulta. Los alumnos se muestran en orden ascendente por apellido. El orden ascendente por apellido es el valor predeterminado (caso de paso explícito) en la instrucción `switch`. Cuando el usuario hace clic en un vínculo de encabezado de columna, se proporciona el valor `sortOrder` correspondiente en el valor de la cadena de consulta.

La instancia de Razor Pages usa `NameSort` y `DateSort` para configurar los hipervínculos del encabezado de columna con los valores de cadena de consulta adecuados:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

El código siguiente contiene el [operador ?:](/dotnet/csharp/language-reference/operators/conditional-operator) condicional de C#:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

La primera línea especifica que, cuando `sortOrder` es NULL o está vacío, `NameSort` se establece en "name_desc". Si `sortOrder`**no** es NULL ni está vacío, `NameSort` se establece en una cadena vacía.

El `?: operator` también se conoce como el operador ternario.

Estas dos instrucciones habilitan la página para establecer los hipervínculos de encabezado de columna de la siguiente forma:

| Criterio de ordenación actual | Hipervínculo de apellido | Hipervínculo de fecha |
|:--------------------:|:-------------------:|:--------------:|
| Apellido: ascendente | descending        | ascending      |
| Apellido: descendente | ascending           | ascending      |
| Fecha: ascendente       | ascending           | descending     |
| Fecha: descendente      | ascending           | ascending      |

El método usa LINQ to Entities para especificar la columna por la que se va a ordenar. El código inicializa un `IQueryable<Student>` antes de la instrucción switch y lo modifica en la instrucción switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Cuando se crea o se modifica un `IQueryable`, no se envía ninguna consulta a la base de datos. La consulta no se ejecuta hasta que el objeto `IQueryable` se convierte en una colección. `IQueryable` se convierte en una colección mediante una llamada a un método como `ToListAsync`. Por lo tanto, el código `IQueryable` produce una única consulta que no se ejecuta hasta la siguiente instrucción:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` se podría detallar con un gran número de columnas ordenables.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Agregar hipervínculos de encabezado de columna a la página de índice de Student

Reemplace el código de *Students/Index.cshtml* con el siguiente código resaltado:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

El código anterior:

* Agrega hipervínculos a los encabezados de columna `LastName` y `EnrollmentDate`.
* Usa la información de `NameSort` y `DateSort` para configurar hipervínculos con los valores de criterio de ordenación actuales.

Para comprobar que la ordenación funciona:

* Ejecute la aplicación y haga clic en la pestaña **Students**.
* Haga clic en **Last Name**.
* Haga clic en **Enrollment Date**.

Para comprender mejor el código:

* En *Student/Index.cshtml.cs* , establezca un punto de interrupción en `switch (sortOrder)`.
* Agregue una inspección para `NameSort` y `DateSort`.
* En *Student/Index.cshtml* , establezca un punto de interrupción en `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Ejecute paso a paso el depurador.

## <a name="add-a-search-box-to-the-students-index-page"></a>Agregar un cuadro de búsqueda a la página de índice de Students

Para agregar un filtro a la página de índice de Students:

* Se agregan un cuadro de texto y un botón de envío a la página de Razor. El cuadro de texto proporciona una cadena de búsqueda de nombre o apellido.
* El modelo de página se actualiza para usar el valor del cuadro de texto.

### <a name="add-filtering-functionality-to-the-index-method"></a>Agregar la funcionalidad de filtrado al método Index

Actualice el elemento `OnGetAsync` de *Students/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

El código anterior:

* Agrega el parámetro `searchString` al método `OnGetAsync`. El valor de la cadena de búsqueda se recibe desde un cuadro de texto que se agrega en la siguiente sección.
* Se agregó una cláusula `Where` a la instrucción LINQ. La cláusula `Where` selecciona solo los alumnos cuyo nombre o apellido contienen la cadena de búsqueda. La instrucción LINQ se ejecuta solo si hay un valor para buscar.

Nota: El código anterior llama al método `Where` en un objeto `IQueryable` y el filtro se procesa en el servidor. En algunos escenarios, la aplicación puede hacer una llamada al método `Where` como un método de extensión en una colección en memoria. Por ejemplo, suponga que `_context.Students` cambia de `DbSet` de EF Core a un método de repositorio que devuelve una colección `IEnumerable`. Lo más habitual es que el resultado fuera el mismo, pero en algunos casos puede ser diferente.

Por ejemplo, la implementación de .NET Framework de `Contains` realiza una comparación que distingue mayúsculas de minúsculas de forma predeterminada. En SQL Server, la distinción entre mayúsculas y minúsculas de `Contains` viene determinada por la configuración de intercalación de la instancia de SQL Server. SQL Server no diferencia entre mayúsculas y minúsculas de forma predeterminada. Se podría llamar a `ToUpper` para hacer explícitamente que la prueba no distinga entre mayúsculas y minúsculas:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

El código anterior garantiza que los resultados no distingan entre mayúsculas y minúsculas si cambia el código para que use `IEnumerable`. Cuando se llama a `Contains` en una colección `IEnumerable`, se usa la implementación de .NET Core. Cuando se llama a `Contains` en un objeto `IQueryable`, se usa la implementación de la base de datos. Devolver un `IEnumerable` desde un repositorio puede acarrear una disminución significativa del rendimiento:

1. Todas las filas se devuelven desde el servidor de base de datos.
1. El filtro se aplica a todas las filas devueltas en la aplicación.

Hay una disminución del rendimiento por llamar a `ToUpper`. El código `ToUpper` agrega una función en la cláusula WHERE de la instrucción SELECT de TSQL. La función agregada impide que el optimizador use un índice. Dado que SQL está instalado para no distinguir entre mayúsculas y minúsculas, es mejor evitar llamar a `ToUpper` cuando no sea necesario.

### <a name="add-a-search-box-to-the-student-index-page"></a>Agregar un cuadro de búsqueda a la página de índice de Student

En *Pages/Student/Index.cshtml* , agregue el siguiente código resaltado para crear un botón **Search** y cromo ordenado.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

El código anterior usa el [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) `<form>` para agregar el cuadro de texto de búsqueda y el botón. De forma predeterminada, el asistente de etiquetas `<form>` envía datos de formulario con POST. Con POST, los parámetros se pasan en el cuerpo del mensaje HTTP y no en la dirección URL. Cuando se usa el método HTTP GET, los datos del formulario se pasan en la dirección URL como cadenas de consulta. Pasar los datos con cadenas de consulta permite a los usuarios marcar la dirección URL. Las [directrices de W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recomiendan el uso de GET cuando la acción no produzca ninguna actualización.

Pruebe la aplicación:

* Seleccione la pestaña **Students** y escriba una cadena de búsqueda.
* Seleccione **Search**.

Fíjese en que la dirección URL contiene la cadena de búsqueda.

```html
http://localhost:5000/Students?SearchString=an
```

Si se colocó un marcador en la página, el marcador contiene la dirección URL a la página y la cadena de consulta de `SearchString`. El `method="get"` en la etiqueta `form` es lo que ha provocado que se generara la cadena de consulta.

Actualmente, cuando se selecciona un vínculo de ordenación del encabezado de columna, el filtro de valor del cuadro **Search** se pierde. El valor de filtro perdido se fija en la sección siguiente.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Agregar la funcionalidad de paginación a la página de índice de Students

En esta sección, se crea una clase `PaginatedList` para admitir la paginación. La clase `PaginatedList` usa las instrucciones `Skip` y `Take` para filtrar los datos en el servidor en lugar de recuperar todas las filas de la tabla. La ilustración siguiente muestra los botones de paginación.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

En la carpeta del proyecto, cree `PaginatedList.cs` con el código siguiente:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

El método `CreateAsync` en el código anterior toma el tamaño y el número de la página, y aplica las instrucciones `Skip` y `Take` correspondientes a `IQueryable`. Cuando `ToListAsync` se llama en `IQueryable`, devuelve una lista que solo contiene la página solicitada. Las propiedades `HasPreviousPage` y `HasNextPage` se usan para habilitar o deshabilitar los botones de página **Previous** y **Next**.

El método `CreateAsync` se usa para crear la `PaginatedList<T>`. No se puede crear un constructor del objeto `PaginatedList<T>`, los constructores no pueden ejecutar código asincrónico.

## <a name="add-paging-functionality-to-the-index-method"></a>Agregar la funcionalidad de paginación al método Index

En *Students/Index.cshtml.cs* , actualice el tipo de `Student` de `IList<Student>` a `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Actualice el elemento `OnGetAsync` de *Students/Index.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

El código anterior agrega el índice de la página, el `sortOrder` actual y el `currentFilter` a la firma del método.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Todos los parámetros son NULL cuando:

* Se llama a la página desde el vínculo **Students**.
* El usuario no ha seleccionado un vínculo de ordenación o paginación.

Cuando se hace clic en un vínculo de paginación, la variable de índice de página contiene el número de página que se tiene que mostrar.

`CurrentSort` proporciona a la página de Razor el criterio de ordenación actual. Se debe incluir el criterio de ordenación actual en los vínculos de paginación para mantener el criterio de ordenación durante la paginación.

`CurrentFilter` proporciona a la página de Razor la cadena de filtrado actual. El valor `CurrentFilter`:

* Debe incluirse en los vínculos de paginación para mantener la configuración del filtro durante la paginación.
* Debe restaurarse en el cuadro de texto cuando se vuelva a mostrar la página.

Si se cambia la cadena de búsqueda durante la paginación, la página se restablece a 1. La página debe restablecerse a 1 porque el nuevo filtro puede hacer que se muestren diferentes datos. Cuando se escribe un valor de búsqueda y se selecciona **Submit** :

* La cadena de búsqueda cambia.
* El parámetro `searchString` no es NULL.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

El método `PaginatedList.CreateAsync` convierte la consulta del alumno en una sola página de alumnos de un tipo de colección que admita la paginación. Esa única página de alumnos se pasa a la página de Razor.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Los dos signos de interrogación en `PaginatedList.CreateAsync` representan el [operador de uso combinado de NULL](/dotnet/csharp/language-reference/operators/null-conditional-operator). El operador de uso combinado de NULL define un valor predeterminado para un tipo que acepta valores NULL. La expresión `(pageIndex ?? 1)` significa devolver el valor de `pageIndex` si tiene un valor. Devuelve 1 si `pageIndex` no tiene ningún valor.

## <a name="add-paging-links-to-the-student-no-locrazor-page"></a>Incorporación de vínculos de paginación a la página de Razor de alumnos

Actualice el marcado en *Students/Index.cshtml*. Se resaltan los cambios:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Los vínculos del encabezado de la columna usan la cadena de consulta para pasar la cadena de búsqueda actual al método `OnGetAsync`, de modo que el usuario pueda ordenar los resultados del filtro:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Los botones de paginación se muestran mediante asistentes de etiquetas:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Ejecute la aplicación y vaya a la página Students.

* Para comprobar que la paginación funciona correctamente, haga clic en los vínculos de paginación en distintos criterios de ordenación.
* Para comprobar que la paginación también funciona correctamente con filtrado y ordenación, escriba una cadena de búsqueda e intente llevar a cabo la paginación de nuevo.

![Página de índice de Students con vínculos de paginación](sort-filter-page/_static/paging.png)

Para comprender mejor el código:

* En *Student/Index.cshtml.cs* , establezca un punto de interrupción en `switch (sortOrder)`.
* Agregue una inspección para `NameSort`, `DateSort`, `CurrentSort` y `Model.Student.PageIndex`.
* En *Student/Index.cshtml* , establezca un punto de interrupción en `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Ejecute paso a paso el depurador.

## <a name="update-the-about-page-to-show-student-statistics"></a>Actualizar la página About para mostrar las estadísticas de los alumnos

En este paso, se actualiza *Pages/About.cshtml* para mostrar cuántos alumnos se han inscrito por cada fecha de inscripción. La actualización usa la agrupación e incluye los siguientes pasos:

* Cree un modelo de vista para los datos usados por la página **About**.
* Actualice la página About para usar el modelo de vista.

### <a name="create-the-view-model"></a>Creación del modelo de vista

Cree una carpeta *SchoolViewModels* en la carpeta *Models*.

En la carpeta *SchoolViewModels* , agregue *EnrollmentDateGroup.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Actualizar el modelo de la página About

Las plantillas web de ASP.NET Core 2.2 no incluyen la página About. Si usa ASP.NET Core 2.2, cree la página de Razor About.

Actualice el archivo *Pages/About.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

La instrucción LINQ agrupa las entidades de alumnos por fecha de inscripción, calcula la cantidad de entidades que se incluyen en cada grupo y almacena los resultados en una colección de objetos de modelo de la vista `EnrollmentDateGroup`.

### <a name="modify-the-about-no-locrazor-page"></a>Modificación de la página de Razor About

Reemplace el código del archivo *Pages/About.cshtml* por el código siguiente:

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

Ejecute la aplicación y vaya a la página About. En una tabla se muestra el número de alumnos para cada fecha de inscripción.

Si experimenta problemas que no puede resolver, descargue la [aplicación completada para esta fase](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Página About](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Recursos adicionales

* [Depuración del código fuente de ASP.NET Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=MDs7PFpoMqI)

En el tutorial siguiente, la aplicación usa las migraciones para actualizar el modelo de datos.

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/crud)
> [Siguiente](xref:data/ef-rp/migrations)

::: moniker-end

