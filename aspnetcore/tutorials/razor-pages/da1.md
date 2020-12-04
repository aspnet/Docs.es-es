---
title: Parte 5, actualización de las páginas generadas
author: rick-anderson
description: Parte 5 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/20/2020
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
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419972"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="19cb2-103">Parte 5. Actualización de las páginas generadas en una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19cb2-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="19cb2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="19cb2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="19cb2-105">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="19cb2-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="19cb2-106">**ReleaseDate** debe tener dos palabras: **Release Date** (Fecha lanzamiento).</span><span class="sxs-lookup"><span data-stu-id="19cb2-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="19cb2-108">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="19cb2-108">Update the generated code</span></span>

<span data-ttu-id="19cb2-109">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19cb2-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="19cb2-110">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="19cb2-110">In the previous code:</span></span>

* <span data-ttu-id="19cb2-111">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19cb2-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="19cb2-112">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="19cb2-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="19cb2-113">El atributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica el nombre para mostrar de un campo.</span><span class="sxs-lookup"><span data-stu-id="19cb2-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="19cb2-114">En el código anterior, "Release Date" en lugar de "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="19cb2-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="19cb2-115">El atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de datos (`Date`).</span><span class="sxs-lookup"><span data-stu-id="19cb2-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="19cb2-116">No se muestra la información de hora almacenada en el campo.</span><span class="sxs-lookup"><span data-stu-id="19cb2-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="19cb2-117">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="19cb2-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="19cb2-118">Vaya a *Pages/Movies* y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="19cb2-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="19cb2-120">Los vínculos de **edición**, **detalles** y **eliminación** se generan mediante el [Asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) en el archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="19cb2-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="19cb2-121">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="19cb2-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="19cb2-122">En el código anterior, el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor Pages (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="19cb2-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="19cb2-123">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="19cb2-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="19cb2-124">Use **Ver código fuente** en un explorador para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="19cb2-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="19cb2-125">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="19cb2-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="19cb2-126">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="19cb2-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="19cb2-127">Por ejemplo, `?id=1` en `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="19cb2-128">Adición de la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="19cb2-128">Add route template</span></span>

<span data-ttu-id="19cb2-129">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta `{id:int}`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="19cb2-130">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="19cb2-131">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="19cb2-131">Run the app and then view source.</span></span>

<span data-ttu-id="19cb2-132">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="19cb2-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="19cb2-133">Una solicitud a la página con la plantilla de ruta `{id:int}` que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="19cb2-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="19cb2-134">Por ejemplo, `https://localhost:5001/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="19cb2-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="19cb2-135">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="19cb2-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="19cb2-136">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="19cb2-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="19cb2-137">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="19cb2-138">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="19cb2-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="19cb2-139">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="19cb2-140">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="19cb2-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="19cb2-141">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="19cb2-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="19cb2-142">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="19cb2-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="19cb2-143">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="19cb2-143">Review concurrency exception handling</span></span>

<span data-ttu-id="19cb2-144">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="19cb2-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="19cb2-145">El código anterior detecta las excepciones de simultaneidad cuando un cliente elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="19cb2-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="19cb2-146">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="19cb2-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="19cb2-147">establezca un punto de interrupción en `catch (DbUpdateConcurrencyException)`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="19cb2-148">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="19cb2-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="19cb2-149">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="19cb2-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="19cb2-150">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="19cb2-151">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="19cb2-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="19cb2-152">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="19cb2-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="19cb2-153">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="19cb2-153">Posting and binding review</span></span>

<span data-ttu-id="19cb2-154">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="19cb2-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="19cb2-155">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="19cb2-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="19cb2-156">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="19cb2-157">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="19cb2-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="19cb2-158">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="19cb2-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="19cb2-159">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="19cb2-160">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="19cb2-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="19cb2-161">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="19cb2-162">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="19cb2-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="19cb2-163">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se vuelve a mostrar con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="19cb2-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="19cb2-164">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="19cb2-165">Los métodos GET HTTP de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="19cb2-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="19cb2-166">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="19cb2-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19cb2-167">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="19cb2-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="19cb2-168">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="19cb2-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="19cb2-169">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="19cb2-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="19cb2-170">**ReleaseDate** debe tener dos palabras: **Release Date** (Fecha lanzamiento).</span><span class="sxs-lookup"><span data-stu-id="19cb2-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="19cb2-172">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="19cb2-172">Update the generated code</span></span>

<span data-ttu-id="19cb2-173">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19cb2-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="19cb2-174">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19cb2-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="19cb2-175">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="19cb2-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="19cb2-176">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="19cb2-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="19cb2-177">El atributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="19cb2-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="19cb2-178">El atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica el tipo de los datos (`Date`), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="19cb2-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="19cb2-179">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="19cb2-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="19cb2-181">Los vínculos de **edición**, **detalles** y **eliminación** se generan mediante el [Asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) en el archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="19cb2-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="19cb2-182">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="19cb2-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="19cb2-183">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="19cb2-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="19cb2-184">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="19cb2-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="19cb2-185">Use **Ver código fuente** en un explorador para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="19cb2-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="19cb2-186">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="19cb2-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="19cb2-187">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="19cb2-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="19cb2-188">Por ejemplo, `?id=1` en `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="19cb2-189">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="19cb2-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="19cb2-190">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="19cb2-191">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="19cb2-191">Run the app and then view source.</span></span> <span data-ttu-id="19cb2-192">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="19cb2-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="19cb2-193">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="19cb2-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="19cb2-194">Por ejemplo, `https://localhost:5001/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="19cb2-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="19cb2-195">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="19cb2-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="19cb2-196">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="19cb2-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="19cb2-197">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="19cb2-198">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="19cb2-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="19cb2-199">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="19cb2-200">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="19cb2-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="19cb2-201">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="19cb2-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="19cb2-202">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="19cb2-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="19cb2-203">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="19cb2-203">Review concurrency exception handling</span></span>

<span data-ttu-id="19cb2-204">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="19cb2-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="19cb2-205">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="19cb2-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="19cb2-206">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="19cb2-206">To test the `catch` block:</span></span>

* <span data-ttu-id="19cb2-207">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="19cb2-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="19cb2-208">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="19cb2-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="19cb2-209">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="19cb2-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="19cb2-210">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="19cb2-211">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="19cb2-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="19cb2-212">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="19cb2-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="19cb2-213">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="19cb2-213">Posting and binding review</span></span>

<span data-ttu-id="19cb2-214">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="19cb2-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="19cb2-215">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="19cb2-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="19cb2-216">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="19cb2-217">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="19cb2-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="19cb2-218">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="19cb2-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="19cb2-219">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="19cb2-220">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="19cb2-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="19cb2-221">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="19cb2-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="19cb2-222">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="19cb2-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="19cb2-223">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se muestra con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="19cb2-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="19cb2-224">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="19cb2-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="19cb2-225">Los métodos GET HTTP de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="19cb2-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="19cb2-226">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="19cb2-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="19cb2-227">La búsqueda se incluye en el tutorial siguiente.</span><span class="sxs-lookup"><span data-stu-id="19cb2-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19cb2-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="19cb2-228">Additional resources</span></span>

* [<span data-ttu-id="19cb2-229">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="19cb2-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="19cb2-230">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="19cb2-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
