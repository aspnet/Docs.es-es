---
title: Parte 6, adición de búsqueda
author: rick-anderson
description: Parte 6 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 1e571966b78f93e29e7901dd9648fbe3aca52726
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855487"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="88879-103">Parte 6. Adición de búsqueda a Razor Pages de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88879-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="88879-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="88879-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="88879-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88879-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="88879-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88879-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88879-107">En las secciones siguientes, se ha agregado la función de buscar películas por *género* o *nombre*.</span><span class="sxs-lookup"><span data-stu-id="88879-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="88879-108">Agregue la siguiente instrucción using resaltada y las propiedades a *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="88879-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="88879-109">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="88879-109">In the previous code:</span></span>

* <span data-ttu-id="88879-110">`SearchString`: contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="88879-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="88879-111">`SearchString` tiene el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="88879-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="88879-112">`[BindProperty]` enlaza los valores del formulario y las cadenas de consulta con el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="88879-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="88879-113">`[BindProperty(SupportsGet = true)]`bse necesita para el enlace de las solicitudes GET de HTTP.</span><span class="sxs-lookup"><span data-stu-id="88879-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="88879-114">`Genres`: contiene la lista de géneros.</span><span class="sxs-lookup"><span data-stu-id="88879-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="88879-115">`Genres` permite al usuario seleccionar un género de la lista.</span><span class="sxs-lookup"><span data-stu-id="88879-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="88879-116">`SelectList` requiere `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="88879-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="88879-117">`MovieGenre`: Contiene el género específico que el usuario selecciona.</span><span class="sxs-lookup"><span data-stu-id="88879-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="88879-118">Por ejemplo, "Western".</span><span class="sxs-lookup"><span data-stu-id="88879-118">For example, "Western".</span></span>
* <span data-ttu-id="88879-119">`Genres` y `MovieGenre` se utilizan posteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="88879-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="88879-120">Actualice el método Index de la página `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="88879-121">La primera línea del método `OnGetAsync` crea una consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para seleccionar las películas:</span><span class="sxs-lookup"><span data-stu-id="88879-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="88879-122">En este momento, solo ***se define** _ la conulta, _*_no_\*_ se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88879-122">The query is only ***defined** _ at this point, it has _*_not_\*_ been run against the database.</span></span>

<span data-ttu-id="88879-123">Si la propiedad `SearchString` no es NULL ni está vacía, la consulta de películas se modifica para filtrar según la cadena de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="88879-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="88879-124">El código `s => s.Title.Contains()` es una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="88879-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="88879-125">Las lambdas se usan en consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basadas en métodos como argumentos para métodos de operador de consulta estándar, tales como el método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains`.</span><span class="sxs-lookup"><span data-stu-id="88879-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="88879-126">Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método como `Where`, `Contains` o `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="88879-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="88879-127">En su lugar, se aplaza la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="88879-128">La evaluación de una expresión se aplaza hasta que su valor realizado se repite o se llama al método `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="88879-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="88879-129">Para más información, vea [Query Execution (Ejecución de consultas)](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="88879-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="88879-130">El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="88879-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="88879-131">La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación.</span><span class="sxs-lookup"><span data-stu-id="88879-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="88879-132">En SQL Server, `Contains` se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="88879-133">En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="88879-134">Vaya a la página de películas y anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="88879-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="88879-135">Por ejemplo, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="88879-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="88879-136">Se muestran las películas filtradas.</span><span class="sxs-lookup"><span data-stu-id="88879-136">The filtered movies are displayed.</span></span>

![Vista Index](search/_static/ghost.png)

<span data-ttu-id="88879-138">Si se agrega la siguiente plantilla de ruta a la página Index, la cadena de búsqueda se puede pasar como un segmento de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="88879-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="88879-139">Por ejemplo, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="88879-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="88879-140">La restricción de ruta anterior permite buscar el título como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="88879-141">El elemento `?` de `"{searchString?}"` significa que se trata de un parámetro de ruta opcional.</span><span class="sxs-lookup"><span data-stu-id="88879-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vista Index con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de películas devuelta con dos películas, Ghostbusters y Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="88879-143">El entorno de ejecución de ASP.NET Core usa el [enlace de modelos](xref:mvc/models/model-binding) para establecer el valor de la propiedad `SearchString` de la cadena de consulta (`?searchString=Ghost`) o de los datos de ruta (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="88879-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="88879-144">El enlace de modelos _*_no_*_ distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-144">Model binding is _*_not_*_ case sensitive.</span></span>

<span data-ttu-id="88879-145">Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL para buscar una película.</span><span class="sxs-lookup"><span data-stu-id="88879-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="88879-146">En este paso, se agrega la interfaz de usuario para filtrar las películas.</span><span class="sxs-lookup"><span data-stu-id="88879-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="88879-147">Si ha agregado la restricción de ruta `"{searchString?}"`, quítela.</span><span class="sxs-lookup"><span data-stu-id="88879-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="88879-148">Abra el archivo _Pages/Movies/Index.cshtml\* y agregue el marcado resaltado en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="88879-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="88879-149">La etiqueta HTML `<form>` usa los siguientes [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="88879-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="88879-150">[Asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="88879-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="88879-151">Cuando se envía el formulario, la cadena de filtro se envía a la página *Pages/Movies/Index* a través de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="88879-152">Asistente de etiquetas de entrada</span><span class="sxs-lookup"><span data-stu-id="88879-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="88879-153">Guarde los cambios y pruebe el filtro.</span><span class="sxs-lookup"><span data-stu-id="88879-153">Save the changes and test the filter.</span></span>

![Vista Index con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="88879-155">Búsqueda por género</span><span class="sxs-lookup"><span data-stu-id="88879-155">Search by genre</span></span>

<span data-ttu-id="88879-156">Actualice el método Index de la página `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="88879-157">El código siguiente es una consulta LINQ que recupera todos los géneros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88879-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="88879-158">La `SelectList` de géneros se crea mediante la proyección de los distintos géneros.</span><span class="sxs-lookup"><span data-stu-id="88879-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="88879-159">Incorporación de búsqueda por género a una página de Razor</span><span class="sxs-lookup"><span data-stu-id="88879-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="88879-160">Actualice *Index.cshtml* [elemento `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form) como se resalta en el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="88879-161">Pruebe la aplicación al buscar por género, título de la película y ambos.</span><span class="sxs-lookup"><span data-stu-id="88879-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88879-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="88879-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="88879-163">[Anterior: Actualización de las páginas](xref:tutorials/razor-pages/da1)
> [Siguiente: Adición de un nuevo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="88879-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88879-164">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="88879-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="88879-165">En las secciones siguientes, se ha agregado la función de buscar películas por *género* o *nombre*.</span><span class="sxs-lookup"><span data-stu-id="88879-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="88879-166">Agregue las siguientes propiedades resaltadas a *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="88879-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="88879-167">`SearchString`: contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="88879-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="88879-168">`SearchString` tiene el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute).</span><span class="sxs-lookup"><span data-stu-id="88879-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="88879-169">`[BindProperty]` enlaza los valores del formulario y las cadenas de consulta con el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="88879-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="88879-170">`[BindProperty(SupportsGet = true)]`bse necesita para el enlace de las solicitudes GET de HTTP.</span><span class="sxs-lookup"><span data-stu-id="88879-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="88879-171">`Genres`: contiene la lista de géneros.</span><span class="sxs-lookup"><span data-stu-id="88879-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="88879-172">`Genres` permite al usuario seleccionar un género de la lista.</span><span class="sxs-lookup"><span data-stu-id="88879-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="88879-173">`SelectList` requiere `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="88879-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="88879-174">`MovieGenre`: Contiene el género específico que el usuario selecciona.</span><span class="sxs-lookup"><span data-stu-id="88879-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="88879-175">Por ejemplo, "Western".</span><span class="sxs-lookup"><span data-stu-id="88879-175">For example, "Western".</span></span>
* <span data-ttu-id="88879-176">`Genres` y `MovieGenre` se utilizan posteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="88879-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="88879-177">Actualice el método Index de la página `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="88879-178">La primera línea del método `OnGetAsync` crea una consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para seleccionar las películas:</span><span class="sxs-lookup"><span data-stu-id="88879-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="88879-179">En este momento *solo* se define la consulta, **no** se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88879-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="88879-180">Si la propiedad `SearchString` no es NULL ni está vacía, la consulta de películas se modifica para filtrar según la cadena de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="88879-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="88879-181">El código `s => s.Title.Contains()` es una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="88879-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="88879-182">Las lambdas se usan en consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basadas en métodos como argumentos para métodos de operador de consulta estándar, tales como el método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains`.</span><span class="sxs-lookup"><span data-stu-id="88879-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="88879-183">Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método como `Where`, `Contains` o `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="88879-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="88879-184">En su lugar, se aplaza la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="88879-185">La evaluación de una expresión se aplaza hasta que su valor realizado se repite o se llama al método `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="88879-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="88879-186">Para más información, vea [Query Execution (Ejecución de consultas)](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="88879-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="88879-187">**Nota:** El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="88879-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="88879-188">La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación.</span><span class="sxs-lookup"><span data-stu-id="88879-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="88879-189">En SQL Server, `Contains` se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="88879-190">En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="88879-191">Vaya a la página de películas y anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="88879-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="88879-192">Por ejemplo, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="88879-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="88879-193">Se muestran las películas filtradas.</span><span class="sxs-lookup"><span data-stu-id="88879-193">The filtered movies are displayed.</span></span>

![Vista Index](search/_static/ghost.png)

<span data-ttu-id="88879-195">Si se agrega la siguiente plantilla de ruta a la página Index, la cadena de búsqueda se puede pasar como un segmento de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="88879-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="88879-196">Por ejemplo, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="88879-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="88879-197">La restricción de ruta anterior permite buscar el título como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="88879-198">El elemento `?` de `"{searchString?}"` significa que se trata de un parámetro de ruta opcional.</span><span class="sxs-lookup"><span data-stu-id="88879-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vista Index con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de películas devuelta con dos películas, Ghostbusters y Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="88879-200">El entorno de ejecución de ASP.NET Core usa el [enlace de modelos](xref:mvc/models/model-binding) para establecer el valor de la propiedad `SearchString` de la cadena de consulta (`?searchString=Ghost`) o de los datos de ruta (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="88879-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="88879-201">El enlace de modelos \**_no_* _ hace distinción entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="88879-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="88879-202">Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL para buscar una película.</span><span class="sxs-lookup"><span data-stu-id="88879-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="88879-203">En este paso, se agrega la interfaz de usuario para filtrar las películas.</span><span class="sxs-lookup"><span data-stu-id="88879-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="88879-204">Si ha agregado la restricción de ruta `"{searchString?}"`, quítela.</span><span class="sxs-lookup"><span data-stu-id="88879-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="88879-205">Abra el archivo _Pages/Movies/Index.cshtml\* y agregue el marcado `<form>` resaltado en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="88879-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="88879-206">La etiqueta HTML `<form>` usa los siguientes [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="88879-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="88879-207">[Asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="88879-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="88879-208">Cuando se envía el formulario, la cadena de filtro se envía a la página *Pages/Movies/Index* a través de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="88879-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="88879-209">Asistente de etiquetas de entrada</span><span class="sxs-lookup"><span data-stu-id="88879-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="88879-210">Guarde los cambios y pruebe el filtro.</span><span class="sxs-lookup"><span data-stu-id="88879-210">Save the changes and test the filter.</span></span>

![Vista Index con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="88879-212">Búsqueda por género</span><span class="sxs-lookup"><span data-stu-id="88879-212">Search by genre</span></span>

<span data-ttu-id="88879-213">Actualice el método `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="88879-214">El código siguiente es una consulta LINQ que recupera todos los géneros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="88879-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="88879-215">La `SelectList` de géneros se crea mediante la proyección de los distintos géneros.</span><span class="sxs-lookup"><span data-stu-id="88879-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="88879-216">Incorporación de búsqueda por género a una página de Razor</span><span class="sxs-lookup"><span data-stu-id="88879-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="88879-217">Actualice *Index.cshtml* [elemento `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form) como se resalta en el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="88879-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="88879-218">Pruebe la aplicación al buscar por género, título de la película y ambos.</span><span class="sxs-lookup"><span data-stu-id="88879-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="88879-219">El código anterior usa el [asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper) y el asistente de etiquetas de opción.</span><span class="sxs-lookup"><span data-stu-id="88879-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88879-220">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="88879-220">Additional resources</span></span>

* [<span data-ttu-id="88879-221">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="88879-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="88879-222">[Anterior: Actualización de las páginas](xref:tutorials/razor-pages/da1)
> [Siguiente: Adición de un nuevo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="88879-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
