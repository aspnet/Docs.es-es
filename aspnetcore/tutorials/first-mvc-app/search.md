---
title: Parte 7. Adición de búsqueda a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: fcb96300c08d9f985cbfe4d3bf055036be7d2eb0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629579"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Parte 7. Adición de búsqueda a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En esta sección agregará capacidad de búsqueda para el método de acción `Index` que permite buscar películas por *género* o *nombre*.

Actualice el método `Index`, que está en *Controllers/MoviesController.cs*, con el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

La primera línea del método de acción `Index` crea una consulta [LINQ](/dotnet/standard/using-linq) para seleccionar las películas:

```csharp
var movies = from m in _context.Movie
             select m;
```

En este momento *solo* se define la consulta, **no** se ejecuta en la base de datos.

Si el parámetro `searchString` contiene una cadena, la consulta de películas se modifica para filtrar según el valor de la cadena de búsqueda:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

El código `s => s.Title.Contains()` anterior es una [expresión Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Las lambdas se usan en consultas [LINQ](/dotnet/standard/using-linq) basadas en métodos como argumentos para métodos de operador de consulta estándar, tales como el método [Where](/dotnet/api/system.linq.enumerable.where) o `Contains` (usado en el código anterior). Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método como `Where`, `Contains` u `OrderBy`. En su lugar, se aplaza la ejecución de la consulta.  Esto significa que la evaluación de una expresión se aplaza hasta que su valor realizado se repita realmente o se llame al método `ToListAsync`. Para más información sobre la ejecución de consultas en diferido, vea [Ejecución de la consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Nota: El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de c# que se muestra arriba. La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación. En SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas. En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.

Navegue a `/Movies/Index`. Anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL. Se muestran las películas filtradas.

![Vista de índice](~/tutorials/first-mvc-app/search/_static/ghost.png)

Si se cambia la firma del método `Index` para que tenga un parámetro con el nombre `id`, el parámetro `id` coincidirá con el marcador `{id}` opcional para el conjunto de rutas predeterminado en *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Cambie el parámetro por `id` y todas las apariciones de `searchString` se modificarán por `id`.

El método `Index` anterior:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

El método `Index` actualizado con el parámetro `id`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Ahora puede pasar el título de la búsqueda como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.

![Vista de índice con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de las películas obtenidas, con dos películas, Ghostbusters y Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL cada vez que quieran buscar una película. Por tanto, ahora deberá agregar elementos de la interfaz de usuario con los que podrán filtrar las películas. Si cambió la firma del método `Index` para probar cómo pasar el parámetro `ID` enlazado a una ruta, vuelva a cambiarlo para que tome un parámetro denominado `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Abra el archivo *Views/Movies/Index.cshtml* y agregue el marcado `<form>` resaltado a continuación:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

La etiqueta HTML `<form>` usa el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms), por lo que cuando se envía el formulario, la cadena de filtro se registra en la acción `Index` del controlador de películas. Guarde los cambios y después pruebe el filtro.

![Vista de índice con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro Title (Título)](~/tutorials/first-mvc-app/search/_static/filter.png)

No hay ninguna sobrecarga `[HttpPost]` del método `Index` como cabría esperar. No es necesario, porque el método no cambia el estado de la aplicación, simplemente filtra los datos.

Después, puede agregar el método `[HttpPost] Index` siguiente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

El parámetro `notUsed` se usa para crear una sobrecarga para el método `Index`. Hablaremos sobre esto más adelante en el tutorial.

Si agrega este método, el invocador de acción coincidiría con el método `[HttpPost] Index`, mientras que el método `[HttpPost] Index` se ejecutaría tal como se muestra en la imagen de abajo.

![Ventana del explorador con la respuesta: From HttpPost Index: filter on ghost (Desde el índice HttpPost: filtrar en Ghost)](~/tutorials/first-mvc-app/search/_static/fo.png)

Sin embargo, aunque agregue esta versión de `[HttpPost]` al método `Index`, hay una limitación en cómo se ha implementado todo esto. Supongamos que quiere marcar una búsqueda en particular o que quiere enviar un vínculo a sus amigos donde puedan hacer clic para ver la misma lista filtrada de películas. Tenga en cuenta que la dirección URL de la solicitud HTTP POST es la misma que la dirección URL de la solicitud GET (localhost:{PUERTO}/Movies/Index): no hay información de búsqueda en la URL. La información de la cadena de búsqueda se envía al servidor como un [valor de campo de formulario](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Puede comprobarlo con las herramientas de desarrollo del explorador o con la excelente [herramienta Fiddler](https://www.telerik.com/fiddler). En la imagen de abajo se muestran las herramientas de desarrollo del explorador Chrome:

![Pestaña Red de las herramientas de desarrollo en Microsoft Edge, que muestra un cuerpo de solicitud con un valor searchString de la palabra Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Puede ver el parámetro de búsqueda y el token [XSRF](xref:security/anti-request-forgery) en el cuerpo de la solicitud. Tenga en cuenta, como se mencionó en el tutorial anterior, que el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms) genera un token [XSRF](xref:security/anti-request-forgery) antifalsificación. Como no se van a modificar datos, no es necesario validar el token con el método del controlador.

El parámetro de búsqueda se encuentra en el cuerpo de solicitud y no en la dirección URL. Por eso no se puede capturar dicha información para marcarla o compartirla con otros usuarios. Para corregir este problema, especifique que la solicitud debe ser `HTTP GET`, que está en el archivo *Views/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Ahora, cuando se envía una búsqueda, la URL contiene la cadena de consulta de búsqueda. La búsqueda también será dirigida al método de acción `HttpGet Index`, aunque tenga un método `HttpPost Index`.

![Ventana del explorador que muestra searchString=ghost en la URL y las películas que se devuelven, Ghostbusters y Ghostbusters 2, que contienen la palabra Ghost (Fantasma)](~/tutorials/first-mvc-app/search/_static/search_get.png)

El marcado siguiente muestra el cambio en la etiqueta `form`:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Adición de búsqueda por género

Agregue la clase `MovieGenreViewModel` siguiente a la carpeta *Models*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

El modelo de vista de película y género contendrá:

* Una lista de películas.
* `SelectList`, que contiene la lista de géneros. Esto permite al usuario seleccionar un género de la lista.
* `MovieGenre`, que contiene el género seleccionado.
* `SearchString`, que contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.

Reemplace el método `Index` en `MoviesController.cs` por el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

El código siguiente es una consulta `LINQ` que recupera todos los géneros de la base de datos.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

La `SelectList` de géneros se crea mediante la proyección de los distintos géneros (no queremos que nuestra lista de selección tenga géneros duplicados).

Cuando el usuario busca el elemento, se conserva el valor de búsqueda en el cuadro de búsqueda.

## <a name="add-search-by-genre-to-the-index-view"></a>Adición de búsqueda por género a la vista de índice

Actualice `Index.cshtml`, que está en *Views/Movies/* , siguiendo estos pasos:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Examine la expresión lambda usada en el siguiente asistente de HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

En el código anterior, el asistente de HTML `DisplayNameFor` inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar. Puesto que la expresión lambda se inspecciona en lugar de evaluarse, no recibirá una infracción de acceso cuando `model`, `model.Movies` o `model.Movies[0]` sean `null` o estén vacíos. Cuando se evalúa la expresión lambda (por ejemplo, `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.

Pruebe la aplicación buscando por género, por título de la película y por ambos:

![Ventana del explorador que muestra los resultados de https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Anterior](controller-methods-views.md)
> [Siguiente](new-field.md)
