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
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360897"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="e62a5-103">Parte 3. Razor Pages con scaffolding en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e62a5-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="e62a5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e62a5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e62a5-105">En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="e62a5-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e62a5-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e62a5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="e62a5-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e62a5-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="e62a5-108">Páginas Create, Delete, de detalles y de edición</span><span class="sxs-lookup"><span data-stu-id="e62a5-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="e62a5-109">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="e62a5-110">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e62a5-111">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="e62a5-112">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar `RazorPagesMovieContext` a la página:</span><span class="sxs-lookup"><span data-stu-id="e62a5-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e62a5-113">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e62a5-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e62a5-114">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="e62a5-115">En la instancia de Razor Pages, se llama a `OnGetAsync` o `OnGet` para inicializar el estado de la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="e62a5-116">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="e62a5-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e62a5-117">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ninguna instrucción de devolución.</span><span class="sxs-lookup"><span data-stu-id="e62a5-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="e62a5-118">Por ejemplo, la página de privacidad:</span><span class="sxs-lookup"><span data-stu-id="e62a5-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="e62a5-119">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="e62a5-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e62a5-120">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="e62a5-121">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="e62a5-122">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e62a5-123">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="e62a5-124">La directiva @page</span><span class="sxs-lookup"><span data-stu-id="e62a5-124">The @page directive</span></span>

<span data-ttu-id="e62a5-125">La directiva `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede administrar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e62a5-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e62a5-126">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e62a5-127">`@page` y `@model` son ejemplos de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="e62a5-128">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e62a5-129">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="e62a5-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e62a5-130">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="e62a5-131">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="e62a5-132">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="e62a5-133">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="e62a5-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="e62a5-134">El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="e62a5-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e62a5-135">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="e62a5-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e62a5-136">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="e62a5-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="e62a5-137">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="e62a5-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="e62a5-138">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="e62a5-138">The layout page</span></span>

<span data-ttu-id="e62a5-139">Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="e62a5-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="e62a5-140">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="e62a5-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="e62a5-141">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e62a5-142">Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e62a5-143">Las plantillas de [diseño](xref:mvc/views/layout) permiten que el diseño del contenedor HTML:</span><span class="sxs-lookup"><span data-stu-id="e62a5-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="e62a5-144">Esté especificado en un solo lugr.</span><span class="sxs-lookup"><span data-stu-id="e62a5-144">Specified in one place.</span></span>
* <span data-ttu-id="e62a5-145">Se aplique en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="e62a5-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="e62a5-146">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e62a5-147">`RenderBody` es un marcador de posición donde se muestran todas las vistas específicas de página, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="e62a5-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e62a5-148">Por ejemplo, seleccione el vínculo **Privacidad** y la vista *Pages/Privacy.cshtml* se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e62a5-149">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="e62a5-149">ViewData and layout</span></span>

<span data-ttu-id="e62a5-150">Tenga en cuenta el siguiente marcado del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e62a5-151">El marcado resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="e62a5-152">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e62a5-153">La clase base `PageModel` contiene una propiedad de diccionario `ViewData` que se puede usar para pasar datos a una vista.</span><span class="sxs-lookup"><span data-stu-id="e62a5-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="e62a5-154">Los objetos se agregan al diccionario `ViewData` con un patrón \***clave-valor** _.</span><span class="sxs-lookup"><span data-stu-id="e62a5-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="e62a5-155">En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e62a5-156">La propiedad `Title` se usa en el archivo _Pages/Shared/_Layout.cshtml\*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="e62a5-157">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="e62a5-158">La línea `@*Markup removed for brevity.*@` es un comentario de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="e62a5-159">A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="e62a5-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="e62a5-160">Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e62a5-161">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="e62a5-161">Update the layout</span></span>

1. <span data-ttu-id="e62a5-162">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e62a5-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="e62a5-163">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="e62a5-164">Reemplace el elemento anterior por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="e62a5-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="e62a5-165">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e62a5-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e62a5-166">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e62a5-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e62a5-167">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="e62a5-168">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="e62a5-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e62a5-169">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="e62a5-170">Guarde los cambios y pruebe la aplicación seleccionando el vínculo **RpMovie** (Película de RP).</span><span class="sxs-lookup"><span data-stu-id="e62a5-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="e62a5-171">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="e62a5-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="e62a5-172">Pruebe los vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** .</span><span class="sxs-lookup"><span data-stu-id="e62a5-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="e62a5-173">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="e62a5-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e62a5-174">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e62a5-175">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e62a5-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="e62a5-176">Consulte este [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="e62a5-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e62a5-177">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e62a5-178">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="e62a5-179">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="e62a5-180">Modelo de página Create</span><span class="sxs-lookup"><span data-stu-id="e62a5-180">The Create page model</span></span>

<span data-ttu-id="e62a5-181">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e62a5-182">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e62a5-183">La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create.</span><span class="sxs-lookup"><span data-stu-id="e62a5-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e62a5-184">Más adelante en el tutorial se muestra un ejemplo del estado de inicialización de `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="e62a5-185">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="e62a5-186">La propiedad `Movie` usa el atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="e62a5-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e62a5-187">Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.</span><span class="sxs-lookup"><span data-stu-id="e62a5-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e62a5-188">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="e62a5-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e62a5-189">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="e62a5-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e62a5-190">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="e62a5-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e62a5-191">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="e62a5-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e62a5-192">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="e62a5-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e62a5-193">Si no hay ningún error de modelo:</span><span class="sxs-lookup"><span data-stu-id="e62a5-193">If there are no model errors:</span></span>

* <span data-ttu-id="e62a5-194">Los datos se guardan.</span><span class="sxs-lookup"><span data-stu-id="e62a5-194">The data is saved.</span></span>
* <span data-ttu-id="e62a5-195">El explorador se redirige a la página Index.</span><span class="sxs-lookup"><span data-stu-id="e62a5-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="e62a5-196">La página Create de una instancia de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e62a5-196">The Create Razor Page</span></span>

<span data-ttu-id="e62a5-197">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="e62a5-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e62a5-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e62a5-199">Visual Studio muestra las etiquetas siguientes con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e62a5-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vista de VS17 de la página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e62a5-201">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e62a5-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e62a5-202">Los asistentes de etiquetas siguientes se muestran en el marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="e62a5-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="e62a5-203">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e62a5-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e62a5-204">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e62a5-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e62a5-205">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e62a5-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="e62a5-206">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="e62a5-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e62a5-207">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="e62a5-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e62a5-208">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="e62a5-209">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="e62a5-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="e62a5-210">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e62a5-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e62a5-211">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e62a5-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e62a5-212">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e62a5-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e62a5-213">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e62a5-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e62a5-214">En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="e62a5-214">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="e62a5-215">[Vea o descargue](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="e62a5-215">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="e62a5-216">Páginas Create, Delete, de detalles y de edición</span><span class="sxs-lookup"><span data-stu-id="e62a5-216">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="e62a5-217">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-217">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="e62a5-218">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-218">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e62a5-219">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-219">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="e62a5-220">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar el `RazorPagesMovieContext` a la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-220">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="e62a5-221">Las páginas con scaffolding siguen este patrón.</span><span class="sxs-lookup"><span data-stu-id="e62a5-221">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="e62a5-222">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e62a5-222">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e62a5-223">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-223">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="e62a5-224">`OnGetAsync` o `OnGet` se invocan en una instancia de Razor Pages para inicializar el estado de esa instancia.</span><span class="sxs-lookup"><span data-stu-id="e62a5-224">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="e62a5-225">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="e62a5-225">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e62a5-226">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ningún método de devolución.</span><span class="sxs-lookup"><span data-stu-id="e62a5-226">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="e62a5-227">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="e62a5-227">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e62a5-228">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-228">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="e62a5-229">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-229">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="e62a5-230">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-230">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e62a5-231">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-231">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="e62a5-232">La directiva de `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e62a5-232">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e62a5-233">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-233">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e62a5-234">`@page` es un ejemplo de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-234">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="e62a5-235">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-235">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e62a5-236">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="e62a5-236">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e62a5-237">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-237">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="e62a5-238">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e62a5-238">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="e62a5-239">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-239">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="e62a5-240">Asistentes de HTML</span><span class="sxs-lookup"><span data-stu-id="e62a5-240">HTML Helpers</span></span>

<span data-ttu-id="e62a5-241">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="e62a5-241">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="e62a5-242">El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="e62a5-242">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e62a5-243">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="e62a5-243">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e62a5-244">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="e62a5-244">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="e62a5-245">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="e62a5-245">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="e62a5-246">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="e62a5-246">The layout page</span></span>

<span data-ttu-id="e62a5-247">Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="e62a5-247">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="e62a5-248">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="e62a5-248">Each page shows the same menu layout.</span></span> <span data-ttu-id="e62a5-249">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-249">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e62a5-250">Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-250">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e62a5-251">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="e62a5-251">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="e62a5-252">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-252">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e62a5-253">`RenderBody` es un marcador de posición donde se mostrarán todas las vistas específicas de página que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="e62a5-253">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e62a5-254">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Pages/Privacy.cshtml** se representará dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-254">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e62a5-255">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="e62a5-255">ViewData and layout</span></span>

<span data-ttu-id="e62a5-256">Tenga en cuenta el siguiente código del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-256">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e62a5-257">El código resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-257">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="e62a5-258">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="e62a5-258">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e62a5-259">La clase base `PageModel` tiene una propiedad de diccionario `ViewData` que se puede usar para agregar datos que quiera pasar a una vista.</span><span class="sxs-lookup"><span data-stu-id="e62a5-259">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="e62a5-260">Puede agregar objetos al diccionario `ViewData` con un patrón de clave/valor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-260">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="e62a5-261">En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-261">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e62a5-262">La propiedad `Title` se usa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-262">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e62a5-263">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-263">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="e62a5-264">La línea `@*Markup removed for brevity.*@` es un comentario de Razor.</span><span class="sxs-lookup"><span data-stu-id="e62a5-264">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="e62a5-265">A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="e62a5-265">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="e62a5-266">Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-266">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e62a5-267">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="e62a5-267">Update the layout</span></span>

<span data-ttu-id="e62a5-268">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e62a5-268">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="e62a5-269">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-269">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="e62a5-270">Reemplace el elemento anterior por el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="e62a5-270">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="e62a5-271">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e62a5-271">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e62a5-272">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e62a5-272">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e62a5-273">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-273">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="e62a5-274">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="e62a5-274">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e62a5-275">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-275">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="e62a5-276">Guarde los cambios y pruebe la aplicación haciendo clic en el vínculo **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="e62a5-276">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="e62a5-277">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="e62a5-277">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="e62a5-278">Pruebe los otros vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** .</span><span class="sxs-lookup"><span data-stu-id="e62a5-278">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="e62a5-279">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="e62a5-279">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e62a5-280">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-280">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e62a5-281">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e62a5-281">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="e62a5-282">Consulte el [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obtener instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="e62a5-282">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e62a5-283">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-283">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e62a5-284">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-284">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="e62a5-285">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="e62a5-285">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="e62a5-286">Modelo de página Create</span><span class="sxs-lookup"><span data-stu-id="e62a5-286">The Create page model</span></span>

<span data-ttu-id="e62a5-287">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-287">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e62a5-288">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="e62a5-288">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e62a5-289">La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create.</span><span class="sxs-lookup"><span data-stu-id="e62a5-289">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e62a5-290">Más adelante en el tutorial veremos el estado de inicialización del método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-290">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="e62a5-291">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e62a5-291">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="e62a5-292">La propiedad `Movie` usa el atributo [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="e62a5-292">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e62a5-293">Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.</span><span class="sxs-lookup"><span data-stu-id="e62a5-293">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e62a5-294">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="e62a5-294">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e62a5-295">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="e62a5-295">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e62a5-296">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="e62a5-296">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e62a5-297">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="e62a5-297">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e62a5-298">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="e62a5-298">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e62a5-299">Si no hay ningún error de modelo, los datos se guardan y el explorador se redirige a la página Index.</span><span class="sxs-lookup"><span data-stu-id="e62a5-299">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="e62a5-300">La página Create de una instancia de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e62a5-300">The Create Razor Page</span></span>

<span data-ttu-id="e62a5-301">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e62a5-301">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="e62a5-302">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e62a5-302">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e62a5-303">Visual Studio muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e62a5-303">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Vista de VS17 de la página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e62a5-305">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e62a5-305">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e62a5-306">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e62a5-306">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e62a5-307">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e62a5-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e62a5-308">Visual Studio para Mac muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e62a5-308">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="e62a5-309">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e62a5-309">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e62a5-310">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e62a5-310">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e62a5-311">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e62a5-311">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="e62a5-312">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="e62a5-312">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e62a5-313">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="e62a5-313">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e62a5-314">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="e62a5-314">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="e62a5-315">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="e62a5-315">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e62a5-316">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e62a5-316">Additional resources</span></span>

* [<span data-ttu-id="e62a5-317">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="e62a5-317">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="e62a5-318">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e62a5-318">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
