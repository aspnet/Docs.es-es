---
title: 'Parte 6. Adición de búsqueda a :::no-loc(Razor)::: Pages de ASP.NET Core'
author: rick-anderson
description: 'Parte 6 de la serie de tutoriales sobre :::no-loc(Razor)::: Pages.'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/search
ms.openlocfilehash: 960f60198f5e65ed05d0374fd0704537376d27d6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058095"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="ba6ca-103">Parte 6. Adición de búsqueda a :::no-loc(Razor)::: Pages de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba6ca-103">Part 6, add search to ASP.NET Core :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="ba6ca-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba6ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="ba6ca-105">En las secciones siguientes, se ha agregado la función de buscar películas por *género* o *nombre*.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="ba6ca-106">Agregue las siguientes propiedades resaltadas a *Pages/Movies/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ba6ca-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="ba6ca-107">`SearchString`: contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="ba6ca-108">`SearchString` tiene el atributo [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="ba6ca-109">`[BindProperty]` enlaza los valores del formulario y las cadenas de consulta con el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="ba6ca-110">`(SupportsGet = true)` se necesita para el enlace de las solicitudes GET.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="ba6ca-111">`Genres`: contiene la lista de géneros.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="ba6ca-112">`Genres` permite al usuario seleccionar un género de la lista.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="ba6ca-113">`SelectList` requiere `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="ba6ca-114">`MovieGenre`: contiene el género concreto que selecciona el usuario (por ejemplo, "Western").</span><span class="sxs-lookup"><span data-stu-id="ba6ca-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="ba6ca-115">`Genres` y `MovieGenre` se utilizan posteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="ba6ca-116">Actualice el método `OnGetAsync` de la página de índice con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="ba6ca-117">La primera línea del método `OnGetAsync` crea una consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para seleccionar las películas:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="ba6ca-118">En este momento *solo* se define la consulta, **no** se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="ba6ca-119">Si la propiedad `SearchString` no es NULL ni está vacía, la consulta de películas se modifica para filtrar según la cadena de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="ba6ca-120">El código `s => s.Title.Contains()` es una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="ba6ca-121">Las lambdas se usan en consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basadas en métodos como argumentos para métodos de operador de consulta estándar, como el método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains` (usado en el código anterior).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="ba6ca-122">Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método (como `Where`, `Contains` u `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="ba6ca-123">En su lugar, se aplaza la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="ba6ca-124">Esto significa que la evaluación de una expresión se aplaza hasta que su valor realizado se repite o se llama al método `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="ba6ca-125">Para más información, vea [Query Execution (Ejecución de consultas)](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ba6ca-126">El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="ba6ca-127">La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="ba6ca-128">En SQL Server, `Contains` se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="ba6ca-129">En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="ba6ca-130">Vaya a la página de películas y anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL (por ejemplo, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="ba6ca-131">Se muestran las películas filtradas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-131">The filtered movies are displayed.</span></span>

![Vista de índice](search/_static/ghost.png)

<span data-ttu-id="ba6ca-133">Si se agrega la siguiente plantilla de ruta a la página de índice, la cadena de búsqueda se puede pasar como un segmento de dirección URL (por ejemplo, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="ba6ca-134">La restricción de ruta anterior permite buscar el título como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="ba6ca-135">El elemento `?` de `"{searchString?}"` significa que se trata de un parámetro de ruta opcional.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de películas devuelta con dos películas, Ghostbusters y Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="ba6ca-137">El entorno de ejecución de ASP.NET Core usa el [enlace de modelos](xref:mvc/models/model-binding) para establecer el valor de la propiedad `SearchString` de la cadena de consulta (`?searchString=Ghost`) o de los datos de ruta (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="ba6ca-138">El enlace de modelos no hace distinción entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="ba6ca-139">Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL para buscar una película.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="ba6ca-140">En este paso, se agrega la interfaz de usuario para filtrar las películas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="ba6ca-141">Si ha agregado la restricción de ruta `"{searchString?}"`, quítela.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="ba6ca-142">Abra el archivo *Pages/Movies/Index.cshtml* y agregue el marcado `<form>` resaltado en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="ba6ca-143">La etiqueta HTML `<form>` usa los siguientes [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="ba6ca-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="ba6ca-144">[Asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ba6ca-145">Cuando se envía el formulario, la cadena de filtro se envía a la página *Pages/Movies/Index* a través de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="ba6ca-146">Asistente de etiquetas de entrada</span><span class="sxs-lookup"><span data-stu-id="ba6ca-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="ba6ca-147">Guarde los cambios y pruebe el filtro.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-147">Save the changes and test the filter.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="ba6ca-149">Búsqueda por género</span><span class="sxs-lookup"><span data-stu-id="ba6ca-149">Search by genre</span></span>

<span data-ttu-id="ba6ca-150">Actualice el método `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="ba6ca-151">El código siguiente es una consulta LINQ que recupera todos los géneros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="ba6ca-152">La `SelectList` de géneros se crea mediante la proyección de los distintos géneros.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="ba6ca-153">Incorporación de búsqueda por género a una página de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="ba6ca-153">Add search by genre to the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="ba6ca-154">Actualice *Index.cshtml* como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="ba6ca-155">Pruebe la aplicación al buscar por género, título de la película y ambos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba6ca-156">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ba6ca-156">Additional resources</span></span>

* [<span data-ttu-id="ba6ca-157">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="ba6ca-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="ba6ca-158">[Anterior: Actualización de páginas](xref:tutorials/razor-pages/da1)
> [Siguiente: ](xref:tutorials/razor-pages/new-field)Adición de un nuevo campo</span><span class="sxs-lookup"><span data-stu-id="ba6ca-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="ba6ca-159">En las secciones siguientes, se ha agregado la función de buscar películas por *género* o *nombre*.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="ba6ca-160">Agregue las siguientes propiedades resaltadas a *Pages/Movies/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ba6ca-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="ba6ca-161">`SearchString`: contiene el texto que los usuarios escriben en el cuadro de texto de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="ba6ca-162">`SearchString` tiene el atributo [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="ba6ca-163">`[BindProperty]` enlaza los valores del formulario y las cadenas de consulta con el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="ba6ca-164">`(SupportsGet = true)` se necesita para el enlace de las solicitudes GET.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="ba6ca-165">`Genres`: contiene la lista de géneros.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="ba6ca-166">`Genres` permite al usuario seleccionar un género de la lista.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="ba6ca-167">`SelectList` requiere `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="ba6ca-168">`MovieGenre`: contiene el género concreto que selecciona el usuario (por ejemplo, "Western").</span><span class="sxs-lookup"><span data-stu-id="ba6ca-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="ba6ca-169">`Genres` y `MovieGenre` se utilizan posteriormente en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="ba6ca-170">Actualice el método `OnGetAsync` de la página de índice con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="ba6ca-171">La primera línea del método `OnGetAsync` crea una consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para seleccionar las películas:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="ba6ca-172">En este momento *solo* se define la consulta, **no** se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="ba6ca-173">Si la propiedad `SearchString` no es NULL ni está vacía, la consulta de películas se modifica para filtrar según la cadena de búsqueda:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="ba6ca-174">El código `s => s.Title.Contains()` es una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="ba6ca-175">Las lambdas se usan en consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basadas en métodos como argumentos para métodos de operador de consulta estándar, como el método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains` (usado en el código anterior).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="ba6ca-176">Las consultas LINQ no se ejecutan cuando se definen ni cuando se modifican mediante una llamada a un método (como `Where`, `Contains` u `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="ba6ca-177">En su lugar, se aplaza la ejecución de la consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="ba6ca-178">Esto significa que la evaluación de una expresión se aplaza hasta que su valor realizado se repite o se llama al método `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="ba6ca-179">Para más información, vea [Query Execution (Ejecución de consultas)](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="ba6ca-180">**Nota:** El método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) se ejecuta en la base de datos, no en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="ba6ca-181">La distinción entre mayúsculas y minúsculas en la consulta depende de la base de datos y la intercalación.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="ba6ca-182">En SQL Server, `Contains` se asigna a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="ba6ca-183">En SQLite, con la intercalación predeterminada, se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="ba6ca-184">Vaya a la página de películas y anexe una cadena de consulta como `?searchString=Ghost` a la dirección URL (por ejemplo, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="ba6ca-185">Se muestran las películas filtradas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-185">The filtered movies are displayed.</span></span>

![Vista de índice](search/_static/ghost.png)

<span data-ttu-id="ba6ca-187">Si se agrega la siguiente plantilla de ruta a la página de índice, la cadena de búsqueda se puede pasar como un segmento de dirección URL (por ejemplo, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="ba6ca-188">La restricción de ruta anterior permite buscar el título como datos de ruta (un segmento de dirección URL) en lugar de como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="ba6ca-189">El elemento `?` de `"{searchString?}"` significa que se trata de un parámetro de ruta opcional.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) agregada a la dirección URL y una lista de películas devuelta con dos películas, Ghostbusters y Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="ba6ca-191">El entorno de ejecución de ASP.NET Core usa el [enlace de modelos](xref:mvc/models/model-binding) para establecer el valor de la propiedad `SearchString` de la cadena de consulta (`?searchString=Ghost`) o de los datos de ruta (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="ba6ca-192">El enlace de modelos no hace distinción entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="ba6ca-193">Sin embargo, no se puede esperar que los usuarios modifiquen la dirección URL para buscar una película.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="ba6ca-194">En este paso, se agrega la interfaz de usuario para filtrar las películas.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="ba6ca-195">Si ha agregado la restricción de ruta `"{searchString?}"`, quítela.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="ba6ca-196">Abra el archivo *Pages/Movies/Index.cshtml* y agregue el marcado `<form>` resaltado en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="ba6ca-197">La etiqueta HTML `<form>` usa los siguientes [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="ba6ca-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="ba6ca-198">[Asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ba6ca-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="ba6ca-199">Cuando se envía el formulario, la cadena de filtro se envía a la página *Pages/Movies/Index* a través de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="ba6ca-200">Asistente de etiquetas de entrada</span><span class="sxs-lookup"><span data-stu-id="ba6ca-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="ba6ca-201">Guarde los cambios y pruebe el filtro.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-201">Save the changes and test the filter.</span></span>

![Vista de índice con la palabra Ghost (Fantasma) escrita en el cuadro de texto del filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="ba6ca-203">Búsqueda por género</span><span class="sxs-lookup"><span data-stu-id="ba6ca-203">Search by genre</span></span>

<span data-ttu-id="ba6ca-204">Actualice el método `OnGetAsync` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="ba6ca-205">El código siguiente es una consulta LINQ que recupera todos los géneros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="ba6ca-206">La `SelectList` de géneros se crea mediante la proyección de los distintos géneros.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="ba6ca-207">Incorporación de búsqueda por género a una página de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="ba6ca-207">Add search by genre to the :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="ba6ca-208">Actualice *Index.cshtml* como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="ba6ca-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="ba6ca-209">Pruebe la aplicación al buscar por género, título de la película y ambos.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="ba6ca-210">El código anterior usa el [asistente de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper) y el asistente de etiquetas de opción.</span><span class="sxs-lookup"><span data-stu-id="ba6ca-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba6ca-211">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ba6ca-211">Additional resources</span></span>

* [<span data-ttu-id="ba6ca-212">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="ba6ca-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="ba6ca-213">[Anterior: Actualización de páginas](xref:tutorials/razor-pages/da1)
> [Siguiente: ](xref:tutorials/razor-pages/new-field)Adición de un nuevo campo</span><span class="sxs-lookup"><span data-stu-id="ba6ca-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
