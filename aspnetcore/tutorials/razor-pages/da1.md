---
title: Parte 5. Actualización de las páginas generadas en una aplicación de ASP.NET Core
author: rick-anderson
description: Parte 5 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 04479e5c3a0b1e9badbb4e58043cf059beefb4ab
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632803"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="e4dc8-103">Parte 5. Actualización de las páginas generadas en una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4dc8-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="e4dc8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4dc8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e4dc8-105">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="e4dc8-106">**FechaDeLanzamiento** debe ser **Fecha de lanzamiento** (tres palabras).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="e4dc8-108">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="e4dc8-108">Update the generated code</span></span>

<span data-ttu-id="e4dc8-109">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="e4dc8-110">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="e4dc8-111">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="e4dc8-112">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="e4dc8-113">El atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="e4dc8-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="e4dc8-114">El atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de los datos (Date), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="e4dc8-115">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="e4dc8-117">Los vínculos **Edit**, **Details** y **Delete** son generados por el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) del archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="e4dc8-118">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="e4dc8-119">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="e4dc8-120">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="e4dc8-121">Use **Ver código fuente** en su explorador preferido para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="e4dc8-122">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="e4dc8-123">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta (por ejemplo, el `?id=1` de `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="e4dc8-124">Adición de la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="e4dc8-124">Add route template</span></span>

<span data-ttu-id="e4dc8-125">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="e4dc8-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="e4dc8-126">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="e4dc8-127">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-127">Run the app and then view source.</span></span> <span data-ttu-id="e4dc8-128">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="e4dc8-129">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="e4dc8-130">Por ejemplo, `http://localhost:5000/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="e4dc8-131">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="e4dc8-132">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="e4dc8-133">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="e4dc8-134">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="e4dc8-135">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="e4dc8-136">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="e4dc8-137">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="e4dc8-138">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="e4dc8-139">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="e4dc8-139">Review concurrency exception handling</span></span>

<span data-ttu-id="e4dc8-140">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="e4dc8-141">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="e4dc8-142">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-142">To test the `catch` block:</span></span>

* <span data-ttu-id="e4dc8-143">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="e4dc8-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="e4dc8-144">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="e4dc8-145">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="e4dc8-146">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="e4dc8-147">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="e4dc8-148">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="e4dc8-149">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="e4dc8-149">Posting and binding review</span></span>

<span data-ttu-id="e4dc8-150">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="e4dc8-151">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="e4dc8-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="e4dc8-152">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="e4dc8-153">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="e4dc8-154">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="e4dc8-155">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="e4dc8-156">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="e4dc8-157">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="e4dc8-158">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="e4dc8-159">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se vuelve a mostrar con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="e4dc8-160">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="e4dc8-161">Los métodos HTTP GET de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="e4dc8-162">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4dc8-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e4dc8-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e4dc8-164">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="e4dc8-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e4dc8-165">La aplicación de películas con scaffolding pinta bien, pero la presentación no es ideal.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="e4dc8-166">**FechaDeLanzamiento** debe ser **Fecha de lanzamiento** (tres palabras).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicación Movie abierta en Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="e4dc8-168">Actualización del código generado</span><span class="sxs-lookup"><span data-stu-id="e4dc8-168">Update the generated code</span></span>

<span data-ttu-id="e4dc8-169">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas mostradas en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="e4dc8-170">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` permite que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="e4dc8-171">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="e4dc8-172">En el próximo tutorial, hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="e4dc8-173">El atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="e4dc8-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="e4dc8-174">El atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de los datos (Date), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="e4dc8-175">Vaya a Pages/Movies y mantenga el mouse sobre un vínculo de **edición** para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="e4dc8-177">Los vínculos **Edit**, **Details** y **Delete** son generados por el [asistente de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) del archivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="e4dc8-178">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="e4dc8-179">En el código anterior, `AnchorTagHelper` genera de forma dinámica el valor del atributo `href` HTML desde la página de Razor (la ruta es relativa), el elemento `asp-page` y el identificador de ruta (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="e4dc8-180">Vea [Generación de direcciones URL para las páginas](xref:razor-pages/index#url-generation-for-pages) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="e4dc8-181">Use **Ver código fuente** en su explorador preferido para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="e4dc8-182">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="e4dc8-183">Los vínculos generados de forma dinámica pasan el identificador de la película con una cadena de consulta (por ejemplo, el `?id=1` de `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="e4dc8-184">Actualice las páginas de Razor Edit, Details y Delete para usar la plantilla de ruta "{id:int}".</span><span class="sxs-lookup"><span data-stu-id="e4dc8-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="e4dc8-185">Cambie la directiva de página de cada una de estas páginas de `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="e4dc8-186">Ejecute la aplicación y luego vea el origen.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-186">Run the app and then view source.</span></span> <span data-ttu-id="e4dc8-187">El HTML generado agrega el identificador a la parte de la ruta de acceso de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="e4dc8-188">Una solicitud a la página con la plantilla de ruta "{id:int}" que **no** incluya el entero devolverá un error HTTP 404 (no encontrado).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="e4dc8-189">Por ejemplo, `http://localhost:5000/Movies/Details` devolverá un error 404.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="e4dc8-190">Para que el identificador sea opcional, anexe `?` a la restricción de ruta:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="e4dc8-191">Para probar el comportamiento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="e4dc8-192">Establezca la directiva de página de *Pages/Movies/Details.cshtml* en `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="e4dc8-193">Establezca un punto de interrupción en `public async Task<IActionResult> OnGetAsync(int? id)` (en *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="e4dc8-194">Navegue a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="e4dc8-195">Con la directiva `@page "{id:int}"`, el punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="e4dc8-196">El motor de enrutamiento devuelve HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="e4dc8-197">Con `@page "{id:int?}"`, el método `OnGetAsync` devuelve `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="e4dc8-198">Revisión del control de excepciones de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="e4dc8-198">Review concurrency exception handling</span></span>

<span data-ttu-id="e4dc8-199">Revise el método `OnPostAsync` en el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="e4dc8-200">El código anterior detecta las excepciones de simultaneidad cuando el cliente uno elimina la película y el otro cliente publica cambios en ella.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="e4dc8-201">Para probar el bloque `catch`:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-201">To test the `catch` block:</span></span>

* <span data-ttu-id="e4dc8-202">Establecer un punto de interrupción en `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="e4dc8-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="e4dc8-203">Seleccione **Editar** para una película y realice cambios, pero no seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="e4dc8-204">En otra ventana del explorador, seleccione el vínculo de **eliminación** de la misma película y luego elimínela.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="e4dc8-205">En la ventana anterior del explorador, publique los cambios en la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="e4dc8-206">Es posible que el código de producción quiera detectar conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="e4dc8-207">Vea [Administración de conflictos de simultaneidad](xref:data/ef-rp/concurrency) para más información.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="e4dc8-208">Revisión de publicaciones y enlaces</span><span class="sxs-lookup"><span data-stu-id="e4dc8-208">Posting and binding review</span></span>

<span data-ttu-id="e4dc8-209">Examine el archivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="e4dc8-210">Cuando se realiza una solicitud HTTP GET a la página Movies/Edit (por ejemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="e4dc8-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="e4dc8-211">El método `OnGetAsync` obtiene la película en la base de datos y devuelve el método `Page`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="e4dc8-212">El método `Page` representa la página de Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="e4dc8-213">El archivo *Pages/Movies/Edit.cshtml* contiene la directiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que hace que el modelo de película esté disponible en la página.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="e4dc8-214">Se abre el formulario de edición con los valores de la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="e4dc8-215">Cuando se publica la página Movies/Edit:</span><span class="sxs-lookup"><span data-stu-id="e4dc8-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="e4dc8-216">Los valores del formulario de la página se enlazan a la propiedad `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="e4dc8-217">El atributo `[BindProperty]` habilita el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="e4dc8-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="e4dc8-218">Si hay errores en el estado del modelo (por ejemplo, `ReleaseDate` no se puede convertir en una fecha), el formulario se muestra con los valores enviados.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="e4dc8-219">Si no hay ningún error en el modelo, se guarda la película.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="e4dc8-220">Los métodos HTTP GET de las páginas de Razor Index, Create y Delete siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="e4dc8-221">El método `OnPostAsync` HTTP POST de la página de Razor Create sigue un patrón similar al del método `OnPostAsync` de la página de Razor Edit.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="e4dc8-222">La búsqueda se incluye en el tutorial siguiente.</span><span class="sxs-lookup"><span data-stu-id="e4dc8-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4dc8-223">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e4dc8-223">Additional resources</span></span>

* [<span data-ttu-id="e4dc8-224">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="e4dc8-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="e4dc8-225">[Anterior: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)
> [Siguiente: Adición de búsqueda](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="e4dc8-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
