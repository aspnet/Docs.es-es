---
title: Parte 5, actualización de las páginas generadas
author: rick-anderson
description: Parte 5 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/20/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 46fbfb50afd03f918f9e02bcc8c1dbde9a080ca4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485945"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="6901b-103">Parte 5. Actualización de las páginas generadas en una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6901b-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="6901b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6901b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6901b-105">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="6901b-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6901b-106">**ReleaseDate** debe tener dos palabras: **Release Date** (Fecha lanzamiento).</span><span class="sxs-lookup"><span data-stu-id="6901b-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6901b-108">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="6901b-108">Update the generated code</span></span>

<span data-ttu-id="6901b-109">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6901b-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6901b-110">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="6901b-110">In the previous code:</span></span>

* <span data-ttu-id="6901b-111">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6901b-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6901b-112">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="6901b-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="6901b-113">El atributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica el nombre para mostrar de un campo.</span><span class="sxs-lookup"><span data-stu-id="6901b-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="6901b-114">En el código anterior, "Release Date" en lugar de "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="6901b-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="6901b-115">El atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="6901b-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6901b-116">No se muestra la información de hora almacenada en el campo.</span><span class="sxs-lookup"><span data-stu-id="6901b-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6901b-117">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="6901b-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="6901b-118">Vaya a *Pages/Movies* y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="6901b-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6901b-120">Los vínculos de **edición**, **detalles** y **eliminación** se generan mediante el [Asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) en el archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6901b-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6901b-121">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="6901b-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="6901b-122">En el código anterior, el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor Pages (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="6901b-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="6901b-123">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="6901b-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="6901b-124">Use **Ver código fuente** en un explorador para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="6901b-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="6901b-125">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="6901b-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="6901b-126">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="6901b-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="6901b-127">Por ejemplo, `?id=1` en `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="6901b-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="6901b-128">Adición de la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="6901b-128">Add route template</span></span>

<span data-ttu-id="6901b-129">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta `{id:int}`.</span><span class="sxs-lookup"><span data-stu-id="6901b-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="6901b-130">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="6901b-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6901b-131">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="6901b-131">Run the app and then view source.</span></span>

<span data-ttu-id="6901b-132">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="6901b-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="6901b-133">Una solicitud a la página con la plantilla de ruta `{id:int}` que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="6901b-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6901b-134">Por ejemplo, `https://localhost:5001/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="6901b-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6901b-135">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="6901b-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6901b-136">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="6901b-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="6901b-137">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="6901b-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="6901b-138">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6901b-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="6901b-139">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="6901b-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6901b-140">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="6901b-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6901b-141">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="6901b-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6901b-142">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="6901b-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6901b-143">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="6901b-143">Review concurrency exception handling</span></span>

<span data-ttu-id="6901b-144">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6901b-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6901b-145">El código anterior detecta las excepciones de simultaneidad cuando un cliente elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="6901b-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6901b-146">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="6901b-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="6901b-147">establezca un punto de interrupción en `catch (DbUpdateConcurrencyException)`.</span><span class="sxs-lookup"><span data-stu-id="6901b-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="6901b-148">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="6901b-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="6901b-149">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="6901b-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="6901b-150">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6901b-151">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="6901b-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6901b-152">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="6901b-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6901b-153">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="6901b-153">Posting and binding review</span></span>

<span data-ttu-id="6901b-154">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6901b-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="6901b-155">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="6901b-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="6901b-156">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="6901b-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="6901b-157">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6901b-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="6901b-158">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="6901b-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6901b-159">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6901b-160">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="6901b-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6901b-161">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6901b-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6901b-162">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="6901b-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6901b-163">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se vuelve a mostrar con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="6901b-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="6901b-164">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6901b-165">Los métodos GET HTTP de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="6901b-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="6901b-166">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="6901b-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6901b-167">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6901b-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6901b-168">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6901b-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6901b-169">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="6901b-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="6901b-170">**ReleaseDate** debe tener dos palabras: **Release Date** (Fecha lanzamiento).</span><span class="sxs-lookup"><span data-stu-id="6901b-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="6901b-172">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="6901b-172">Update the generated code</span></span>

<span data-ttu-id="6901b-173">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="6901b-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="6901b-174">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="6901b-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="6901b-175">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="6901b-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="6901b-176">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="6901b-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="6901b-177">El atributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="6901b-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="6901b-178">El atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de los datos (`Date`), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="6901b-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="6901b-179">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="6901b-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="6901b-181">Los vínculos de **edición**, **detalles** y **eliminación** se generan mediante el [Asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) en el archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6901b-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="6901b-182">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="6901b-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6901b-183">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="6901b-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="6901b-184">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="6901b-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="6901b-185">Use **Ver código fuente** en un explorador para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="6901b-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="6901b-186">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="6901b-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="6901b-187">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="6901b-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="6901b-188">Por ejemplo, `?id=1` en `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="6901b-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="6901b-189">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="6901b-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="6901b-190">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="6901b-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="6901b-191">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="6901b-191">Run the app and then view source.</span></span> <span data-ttu-id="6901b-192">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="6901b-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="6901b-193">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="6901b-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6901b-194">Por ejemplo, `https://localhost:5001/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="6901b-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="6901b-195">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="6901b-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6901b-196">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="6901b-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="6901b-197">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="6901b-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="6901b-198">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6901b-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="6901b-199">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="6901b-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="6901b-200">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="6901b-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="6901b-201">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="6901b-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="6901b-202">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="6901b-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="6901b-203">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="6901b-203">Review concurrency exception handling</span></span>

<span data-ttu-id="6901b-204">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6901b-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6901b-205">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="6901b-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="6901b-206">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="6901b-206">To test the `catch` block:</span></span>

* <span data-ttu-id="6901b-207">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="6901b-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="6901b-208">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="6901b-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="6901b-209">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="6901b-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="6901b-210">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="6901b-211">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="6901b-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="6901b-212">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="6901b-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="6901b-213">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="6901b-213">Posting and binding review</span></span>

<span data-ttu-id="6901b-214">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6901b-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="6901b-215">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="6901b-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="6901b-216">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="6901b-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="6901b-217">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6901b-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="6901b-218">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="6901b-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="6901b-219">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="6901b-220">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="6901b-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="6901b-221">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6901b-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="6901b-222">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="6901b-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="6901b-223">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se muestra con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="6901b-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="6901b-224">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="6901b-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="6901b-225">Los métodos GET HTTP de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="6901b-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="6901b-226">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="6901b-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="6901b-227">La búsqueda se incluye en el tutorial siguiente.</span><span class="sxs-lookup"><span data-stu-id="6901b-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6901b-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6901b-228">Additional resources</span></span>

* [<span data-ttu-id="6901b-229">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="6901b-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="6901b-230">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="6901b-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
