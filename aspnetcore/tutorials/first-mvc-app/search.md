---
title: Parte 7. Adición de búsqueda a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 7 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 65bf5e6b022699268f9e7e5677ea8632f1a489c7
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213137"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="0e097-103">Parte 7. Adición de búsqueda a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0e097-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="0e097-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e097-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0e097-105">En esta sección agregará capacidad de búsqueda para el método de acción `Index` que permite buscar películas por *género* o *nombre*.</span><span class="sxs-lookup"><span data-stu-id="0e097-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="0e097-106">Actualice el método `Index`, que está en *Controllers/MoviesController.cs*, con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e097-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="0e097-107">La primera línea del método de acción `Index` crea una consulta [LINQ](/dotnet/standard/using-linq) para seleccionar las películas:</span><span class="sxs-lookup"><span data-stu-id="0e097-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="0e097-108">En este momento *solo* se define la consulta, **no** se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0e097-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="0e097-109">Si el parámetro `searchString` contiene una cadena, la consulta de películas se modifica para filtrar según el valor de la cadena de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="0e097-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="0e097-110">El código `s => s.Title.Contains()` anterior es una [expresión Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0e097-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0e097-111">Las lambdas se usan en consultas [LINQ](/dotnet/standard/using-linq) basadas en métodos como argumentos para métodos de operador de consulta estándar, tales como el método [Where](/dotnet/api/system.linq.enumerable.where) o `Contains` (usado en el código anterior).</span><span class="sxs-lookup"><span data-stu-id="0e097-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="0e097-112">Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método como `Where`, `Contains` u `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="0e097-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="0e097-113">En su lugar, se aplaza la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="0e097-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="0e097-114">Esto significa que la evaluación de una expresión se aplaza hasta que su valor realizado se repita realmente o se llame al método `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="0e097-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="0e097-115">Para más información sobre la ejecución de consultas en diferido, vea [Ejecución de la consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="0e097-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="0e097-116">Nota: El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de c# que se muestra arriba.</span><span class="sxs-lookup"><span data-stu-id="0e097-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="0e097-117">La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación.</span><span class="sxs-lookup"><span data-stu-id="0e097-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="0e097-118">En SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0e097-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="0e097-119">En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0e097-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="0e097-120">Navegue a `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="0e097-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="0e097-121">Anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="0e097-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="0e097-122">Se muestran las películas filtradas.</span><span class="sxs-lookup"><span data-stu-id="0e097-122">The filtered movies are displayed.</span></span>

![Vista de índice](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="0e097-124">Si se cambia la firma del método `Index` para que tenga un parámetro con el nombre `id`, el parámetro `id` coincidirá con el marcador `{id}` opcional para el conjunto de rutas predeterminado en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0e097-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="0e097-125">Cambie el parámetro por `id` y todas las apariciones de `searchString` se modificarán por `id`.</span><span class="sxs-lookup"><span data-stu-id="0e097-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="0e097-126">El método `Index` anterior:</span><span class="sxs-lookup"><span data-stu-id="0e097-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="0e097-127">El método `Index` actualizado con el parámetro `id`:</span><span class="sxs-lookup"><span data-stu-id="0e097-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="0e097-128">Ahora puede pasar el título de la búsqueda como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="0e097-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de las películas obtenidas, con dos películas, Ghostbusters y Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="0e097-130">Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL cada vez que quieran buscar una película.</span><span class="sxs-lookup"><span data-stu-id="0e097-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="0e097-131">Por tanto, ahora deberá agregar elementos de la interfaz de usuario con los que podrán filtrar las películas.</span><span class="sxs-lookup"><span data-stu-id="0e097-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="0e097-132">Si cambió la firma del método `Index` para probar cómo pasar el parámetro `ID` enlazado a una ruta, vuelva a cambiarlo para que tome un parámetro denominado `searchString`:</span><span class="sxs-lookup"><span data-stu-id="0e097-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="0e097-133">Abra el archivo *Views/Movies/Index.cshtml* y agregue el marcado `<form>` resaltado a continuación:</span><span class="sxs-lookup"><span data-stu-id="0e097-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="0e097-134">La etiqueta HTML `<form>` usa el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms), por lo que cuando se envía el formulario, la cadena de filtro se registra en la acción `Index` del controlador de películas.</span><span class="sxs-lookup"><span data-stu-id="0e097-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="0e097-135">Guarde los cambios y después pruebe el filtro.</span><span class="sxs-lookup"><span data-stu-id="0e097-135">Save your changes and then test the filter.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro Title (Título)](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="0e097-137">No hay ninguna sobrecarga `[HttpPost]` del método `Index` como cabría esperar.</span><span class="sxs-lookup"><span data-stu-id="0e097-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="0e097-138">No es necesario, porque el método no cambia el estado de la aplicación, simplemente filtra los datos.</span><span class="sxs-lookup"><span data-stu-id="0e097-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="0e097-139">Después, puede agregar el método `[HttpPost] Index` siguiente.</span><span class="sxs-lookup"><span data-stu-id="0e097-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="0e097-140">El parámetro `notUsed` se usa para crear una sobrecarga para el método `Index`.</span><span class="sxs-lookup"><span data-stu-id="0e097-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="0e097-141">Hablaremos sobre esto más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="0e097-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="0e097-142">Si agrega este método, el invocador de acción coincidiría con el método `[HttpPost] Index`, mientras que el método `[HttpPost] Index` se ejecutaría tal como se muestra en la imagen de abajo.</span><span class="sxs-lookup"><span data-stu-id="0e097-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Ventana del explorador con la respuesta: From HttpPost Index: filter on ghost (Desde el índice HttpPost: filtrar en Ghost)](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="0e097-144">Sin embargo, aunque agregue esta versión de `[HttpPost]` al método `Index`, hay una limitación en cómo se ha implementado todo esto.</span><span class="sxs-lookup"><span data-stu-id="0e097-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="0e097-145">Supongamos que quiere marcar una búsqueda en particular o que quiere enviar un vínculo a sus amigos donde puedan hacer clic para ver la misma lista filtrada de películas.</span><span class="sxs-lookup"><span data-stu-id="0e097-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="0e097-146">Tenga en cuenta que la dirección URL de la solicitud HTTP POST es la misma que la dirección URL de la solicitud GET (localhost:{PUERTO}/Movies/Index): no hay información de búsqueda en la URL.</span><span class="sxs-lookup"><span data-stu-id="0e097-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="0e097-147">La información de la cadena de búsqueda se envía al servidor como un [valor de campo de formulario](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="0e097-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="0e097-148">Puede comprobarlo con las herramientas de desarrollo del explorador o con la excelente [herramienta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="0e097-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="0e097-149">En la imagen de abajo se muestran las herramientas de desarrollo del explorador Chrome:</span><span class="sxs-lookup"><span data-stu-id="0e097-149">The image below shows the Chrome browser Developer tools:</span></span>

![Pestaña Red de las herramientas de desarrollo en Microsoft Edge, que muestra un cuerpo de solicitud con un valor searchString de la palabra Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="0e097-151">Puede ver el parámetro de búsqueda y el token [XSRF](xref:security/anti-request-forgery) en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="0e097-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="0e097-152">Tenga en cuenta, como se mencionó en el tutorial anterior, que el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms) genera un token [XSRF](xref:security/anti-request-forgery) antifalsificación.</span><span class="sxs-lookup"><span data-stu-id="0e097-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="0e097-153">Como no se van a modificar datos, no es necesario validar el token con el método del controlador.</span><span class="sxs-lookup"><span data-stu-id="0e097-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="0e097-154">El parámetro de búsqueda se encuentra en el cuerpo de solicitud y no en la dirección URL. Por eso no se puede capturar dicha información para marcarla o compartirla con otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="0e097-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="0e097-155">Para corregir este problema, especifique que la solicitud debe ser `HTTP GET`, que está en el archivo *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e097-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="0e097-156">Ahora, cuando se envía una búsqueda, la URL contiene la cadena de consulta de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0e097-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="0e097-157">La búsqueda también será dirigida al método de acción `HttpGet Index`, aunque tenga un método `HttpPost Index`.</span><span class="sxs-lookup"><span data-stu-id="0e097-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Ventana del explorador que muestra searchString=ghost en la URL y las películas que se devuelven, Ghostbusters y Ghostbusters 2, que contienen la palabra Ghost (Fantasma)](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="0e097-159">El marcado siguiente muestra el cambio en la etiqueta `form`:</span><span class="sxs-lookup"><span data-stu-id="0e097-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="0e097-160">Adición de búsqueda por género</span><span class="sxs-lookup"><span data-stu-id="0e097-160">Add Search by genre</span></span>

<span data-ttu-id="0e097-161">Agregue la clase `MovieGenreViewModel` siguiente a la carpeta *Models*:</span><span class="sxs-lookup"><span data-stu-id="0e097-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="0e097-162">El modelo de vista de película y género contendrá:</span><span class="sxs-lookup"><span data-stu-id="0e097-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="0e097-163">Una lista de películas.</span><span class="sxs-lookup"><span data-stu-id="0e097-163">A list of movies.</span></span>
* <span data-ttu-id="0e097-164">`SelectList`, que contiene la lista de géneros.</span><span class="sxs-lookup"><span data-stu-id="0e097-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="0e097-165">Esto permite al usuario seleccionar un género de la lista.</span><span class="sxs-lookup"><span data-stu-id="0e097-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="0e097-166">`MovieGenre`, que contiene el género seleccionado.</span><span class="sxs-lookup"><span data-stu-id="0e097-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="0e097-167">`SearchString`, que contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0e097-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="0e097-168">Reemplace el método `Index` en `MoviesController.cs` por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0e097-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="0e097-169">El código siguiente es una consulta `LINQ` que recupera todos los géneros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0e097-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="0e097-170">La `SelectList` de géneros se crea mediante la proyección de los distintos géneros (no queremos que nuestra lista de selección tenga géneros duplicados).</span><span class="sxs-lookup"><span data-stu-id="0e097-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="0e097-171">Cuando el usuario busca el elemento, se conserva el valor de búsqueda en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0e097-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="0e097-172">Adición de búsqueda por género a la vista de índice</span><span class="sxs-lookup"><span data-stu-id="0e097-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="0e097-173">Actualice `Index.cshtml`, que está en *Views/Movies/* , siguiendo estos pasos:</span><span class="sxs-lookup"><span data-stu-id="0e097-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="0e097-174">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="0e097-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="0e097-175">En el código anterior, el asistente de HTML `DisplayNameFor` inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="0e097-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="0e097-176">Puesto que la expresión lambda se inspecciona en lugar de evaluarse, no recibirá una infracción de acceso cuando `model`, `model.Movies` o `model.Movies[0]` sean `null` o estén vacíos.</span><span class="sxs-lookup"><span data-stu-id="0e097-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="0e097-177">Cuando se evalúa la expresión lambda (por ejemplo, `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="0e097-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="0e097-178">Pruebe la aplicación buscando por género, por título de la película y por ambos:</span><span class="sxs-lookup"><span data-stu-id="0e097-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Ventana del explorador que muestra los resultados de https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="0e097-180">[Anterior](controller-methods-views.md)
> [Siguiente](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="0e097-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
