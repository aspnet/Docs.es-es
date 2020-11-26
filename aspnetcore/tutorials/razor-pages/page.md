---
title: Parte 3, aplicación de scaffolding a Razor Pages
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: d655be26a794f87a0be07046ae1d6415256d592c
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417635"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="1fb01-103">Parte 3. Razor Pages con scaffolding en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1fb01-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1fb01-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1fb01-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1fb01-105">En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="1fb01-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1fb01-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1fb01-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="1fb01-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1fb01-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="1fb01-108">Páginas Create, Delete, de detalles y de edición</span><span class="sxs-lookup"><span data-stu-id="1fb01-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="1fb01-109">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="1fb01-110">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="1fb01-111">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="1fb01-112">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar `RazorPagesMovieContext` a la página:</span><span class="sxs-lookup"><span data-stu-id="1fb01-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="1fb01-113">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1fb01-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="1fb01-114">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="1fb01-115">En la instancia de Razor Pages, se llama a `OnGetAsync` o `OnGet` para inicializar el estado de la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="1fb01-116">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="1fb01-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="1fb01-117">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ninguna instrucción de devolución.</span><span class="sxs-lookup"><span data-stu-id="1fb01-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="1fb01-118">Por ejemplo, la página de privacidad:</span><span class="sxs-lookup"><span data-stu-id="1fb01-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1fb01-119">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="1fb01-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="1fb01-120">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="1fb01-121">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="1fb01-122">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="1fb01-123">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="1fb01-124">La directiva @page</span><span class="sxs-lookup"><span data-stu-id="1fb01-124">The @page directive</span></span>

<span data-ttu-id="1fb01-125">La directiva `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede administrar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1fb01-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="1fb01-126">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1fb01-127">`@page` y `@model` son ejemplos de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="1fb01-128">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="1fb01-129">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="1fb01-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="1fb01-130">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="1fb01-131">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="1fb01-132">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="1fb01-133">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="1fb01-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="1fb01-134">El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="1fb01-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1fb01-135">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="1fb01-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="1fb01-136">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="1fb01-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="1fb01-137">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="1fb01-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="1fb01-138">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="1fb01-138">The layout page</span></span>

<span data-ttu-id="1fb01-139">Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="1fb01-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="1fb01-140">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="1fb01-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="1fb01-141">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1fb01-142">Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1fb01-143">Las plantillas de [diseño](xref:mvc/views/layout) permiten que el diseño del contenedor HTML:</span><span class="sxs-lookup"><span data-stu-id="1fb01-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="1fb01-144">Esté especificado en un solo lugr.</span><span class="sxs-lookup"><span data-stu-id="1fb01-144">Specified in one place.</span></span>
* <span data-ttu-id="1fb01-145">Se aplique en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="1fb01-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="1fb01-146">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1fb01-147">`RenderBody` es un marcador de posición donde se muestran todas las vistas específicas de página, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="1fb01-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1fb01-148">Por ejemplo, seleccione el vínculo **Privacidad** y la vista *Pages/Privacy.cshtml* se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="1fb01-149">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="1fb01-149">ViewData and layout</span></span>

<span data-ttu-id="1fb01-150">Tenga en cuenta el siguiente marcado del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="1fb01-151">El marcado resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="1fb01-152">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="1fb01-153">La clase base `PageModel` contiene una propiedad de diccionario `ViewData` que se puede usar para pasar datos a una vista.</span><span class="sxs-lookup"><span data-stu-id="1fb01-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="1fb01-154">Los objetos se agregan al diccionario `ViewData` con un patrón \***clave-valor** _.</span><span class="sxs-lookup"><span data-stu-id="1fb01-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="1fb01-155">En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="1fb01-156">La propiedad `Title` se usa en el archivo _Pages/Shared/_Layout.cshtml\*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="1fb01-157">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="1fb01-158">La línea `@*Markup removed for brevity.*@` es un comentario de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="1fb01-159">A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="1fb01-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="1fb01-160">Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="1fb01-161">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="1fb01-161">Update the layout</span></span>

1. <span data-ttu-id="1fb01-162">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1fb01-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="1fb01-163">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="1fb01-164">Reemplace el elemento anterior por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="1fb01-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="1fb01-165">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1fb01-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1fb01-166">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1fb01-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="1fb01-167">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="1fb01-168">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="1fb01-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="1fb01-169">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="1fb01-170">Guarde los cambios y pruebe la aplicación seleccionando el vínculo **RpMovie** (Película de RP).</span><span class="sxs-lookup"><span data-stu-id="1fb01-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="1fb01-171">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="1fb01-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="1fb01-172">Pruebe los vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** .</span><span class="sxs-lookup"><span data-stu-id="1fb01-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="1fb01-173">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="1fb01-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="1fb01-174">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1fb01-175">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1fb01-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="1fb01-176">Consulte este [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="1fb01-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="1fb01-177">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1fb01-178">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="1fb01-179">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="1fb01-180">Modelo de página Create</span><span class="sxs-lookup"><span data-stu-id="1fb01-180">The Create page model</span></span>

<span data-ttu-id="1fb01-181">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="1fb01-182">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="1fb01-183">La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create.</span><span class="sxs-lookup"><span data-stu-id="1fb01-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="1fb01-184">Más adelante en el tutorial se muestra un ejemplo del estado de inicialización de `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="1fb01-185">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="1fb01-186">La propiedad `Movie` usa el atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1fb01-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1fb01-187">Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.</span><span class="sxs-lookup"><span data-stu-id="1fb01-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="1fb01-188">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="1fb01-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="1fb01-189">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="1fb01-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="1fb01-190">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="1fb01-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="1fb01-191">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="1fb01-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="1fb01-192">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="1fb01-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="1fb01-193">Si no hay ningún error de modelo:</span><span class="sxs-lookup"><span data-stu-id="1fb01-193">If there are no model errors:</span></span>

* <span data-ttu-id="1fb01-194">Los datos se guardan.</span><span class="sxs-lookup"><span data-stu-id="1fb01-194">The data is saved.</span></span>
* <span data-ttu-id="1fb01-195">El explorador se redirige a la página Index.</span><span class="sxs-lookup"><span data-stu-id="1fb01-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="1fb01-196">La página Create de una instancia de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1fb01-196">The Create Razor Page</span></span>

<span data-ttu-id="1fb01-197">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="1fb01-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fb01-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fb01-199">Visual Studio muestra las etiquetas siguientes con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="1fb01-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vista de VS17 de la página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1fb01-201">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1fb01-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1fb01-202">Los asistentes de etiquetas siguientes se muestran en el marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="1fb01-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="1fb01-203">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1fb01-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="1fb01-204">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="1fb01-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="1fb01-205">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="1fb01-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="1fb01-206">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="1fb01-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="1fb01-207">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="1fb01-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="1fb01-208">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="1fb01-209">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="1fb01-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="1fb01-210">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1fb01-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1fb01-211">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1fb01-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1fb01-212">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="1fb01-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="1fb01-213">Páginas Create, Delete, de detalles y de edición</span><span class="sxs-lookup"><span data-stu-id="1fb01-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="1fb01-214">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="1fb01-215">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="1fb01-216">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="1fb01-217">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar el `RazorPagesMovieContext` a la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="1fb01-218">Las páginas con scaffolding siguen este patrón.</span><span class="sxs-lookup"><span data-stu-id="1fb01-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="1fb01-219">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1fb01-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="1fb01-220">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="1fb01-221">`OnGetAsync` o `OnGet` se invocan en una instancia de Razor Pages para inicializar el estado de esa instancia.</span><span class="sxs-lookup"><span data-stu-id="1fb01-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="1fb01-222">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="1fb01-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="1fb01-223">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ningún método de devolución.</span><span class="sxs-lookup"><span data-stu-id="1fb01-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="1fb01-224">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="1fb01-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="1fb01-225">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="1fb01-226">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="1fb01-227">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="1fb01-228">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="1fb01-229">La directiva de `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1fb01-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="1fb01-230">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1fb01-231">`@page` es un ejemplo de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="1fb01-232">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="1fb01-233">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="1fb01-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="1fb01-234">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="1fb01-235">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1fb01-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="1fb01-236">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="1fb01-237">Asistentes de HTML</span><span class="sxs-lookup"><span data-stu-id="1fb01-237">HTML Helpers</span></span>

<span data-ttu-id="1fb01-238">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="1fb01-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="1fb01-239">El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="1fb01-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1fb01-240">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="1fb01-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="1fb01-241">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="1fb01-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="1fb01-242">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="1fb01-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="1fb01-243">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="1fb01-243">The layout page</span></span>

<span data-ttu-id="1fb01-244">Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="1fb01-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="1fb01-245">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="1fb01-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="1fb01-246">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1fb01-247">Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1fb01-248">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="1fb01-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="1fb01-249">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1fb01-250">`RenderBody` es un marcador de posición donde se mostrarán todas las vistas específicas de página que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="1fb01-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1fb01-251">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Pages/Privacy.cshtml** se representará dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="1fb01-252">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="1fb01-252">ViewData and layout</span></span>

<span data-ttu-id="1fb01-253">Tenga en cuenta el siguiente código del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="1fb01-254">El código resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="1fb01-255">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="1fb01-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="1fb01-256">La clase base `PageModel` tiene una propiedad de diccionario `ViewData` que se puede usar para agregar datos que quiera pasar a una vista.</span><span class="sxs-lookup"><span data-stu-id="1fb01-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="1fb01-257">Puede agregar objetos al diccionario `ViewData` con un patrón de clave/valor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="1fb01-258">En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="1fb01-259">La propiedad `Title` se usa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1fb01-260">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="1fb01-261">La línea `@*Markup removed for brevity.*@` es un comentario de Razor.</span><span class="sxs-lookup"><span data-stu-id="1fb01-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="1fb01-262">A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="1fb01-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="1fb01-263">Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="1fb01-264">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="1fb01-264">Update the layout</span></span>

<span data-ttu-id="1fb01-265">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="1fb01-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="1fb01-266">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="1fb01-267">Reemplace el elemento anterior por el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="1fb01-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="1fb01-268">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1fb01-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="1fb01-269">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1fb01-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="1fb01-270">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="1fb01-271">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="1fb01-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="1fb01-272">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="1fb01-273">Guarde los cambios y pruebe la aplicación haciendo clic en el vínculo **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="1fb01-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="1fb01-274">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="1fb01-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="1fb01-275">Pruebe los otros vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** .</span><span class="sxs-lookup"><span data-stu-id="1fb01-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="1fb01-276">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="1fb01-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="1fb01-277">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1fb01-278">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1fb01-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="1fb01-279">Consulte el [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obtener instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="1fb01-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="1fb01-280">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1fb01-281">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="1fb01-282">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="1fb01-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="1fb01-283">Modelo de página Create</span><span class="sxs-lookup"><span data-stu-id="1fb01-283">The Create page model</span></span>

<span data-ttu-id="1fb01-284">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="1fb01-285">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="1fb01-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="1fb01-286">La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create.</span><span class="sxs-lookup"><span data-stu-id="1fb01-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="1fb01-287">Más adelante en el tutorial veremos el estado de inicialización del método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="1fb01-288">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fb01-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="1fb01-289">La propiedad `Movie` usa el atributo [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1fb01-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1fb01-290">Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.</span><span class="sxs-lookup"><span data-stu-id="1fb01-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="1fb01-291">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="1fb01-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="1fb01-292">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="1fb01-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="1fb01-293">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="1fb01-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="1fb01-294">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="1fb01-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="1fb01-295">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="1fb01-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="1fb01-296">Si no hay ningún error de modelo, los datos se guardan y el explorador se redirige a la página Index.</span><span class="sxs-lookup"><span data-stu-id="1fb01-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="1fb01-297">La página Create de una instancia de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1fb01-297">The Create Razor Page</span></span>

<span data-ttu-id="1fb01-298">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1fb01-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="1fb01-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fb01-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fb01-300">Visual Studio muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="1fb01-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Vista de VS17 de la página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1fb01-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1fb01-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1fb01-303">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="1fb01-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1fb01-304">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1fb01-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1fb01-305">Visual Studio para Mac muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="1fb01-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="1fb01-306">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1fb01-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="1fb01-307">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="1fb01-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="1fb01-308">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="1fb01-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="1fb01-309">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="1fb01-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="1fb01-310">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="1fb01-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="1fb01-311">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="1fb01-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="1fb01-312">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="1fb01-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1fb01-313">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1fb01-313">Additional resources</span></span>

* [<span data-ttu-id="1fb01-314">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="1fb01-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="1fb01-315">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="1fb01-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
