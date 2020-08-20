---
title: Parte 3. Razor Pages con scaffolding en ASP.NET Core
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 08/17/2019
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 9fc44d228a31b8ae6c78dc12988392806dd95823
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633440"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="673a2-103">Parte 3. Razor Pages con scaffolding en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="673a2-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="673a2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="673a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="673a2-105">En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="673a2-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="673a2-106">Páginas de creación, eliminación, detalles y edición</span><span class="sxs-lookup"><span data-stu-id="673a2-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="673a2-107">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="673a2-108">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="673a2-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="673a2-109">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="673a2-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="673a2-110">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar el `RazorPagesMovieContext` a la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="673a2-111">Todas las páginas con scaffolding siguen este patrón.</span><span class="sxs-lookup"><span data-stu-id="673a2-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="673a2-112">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="673a2-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="673a2-113">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="673a2-114">Se llama a `OnGetAsync` o `OnGet` para inicializar el estado de la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="673a2-115">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="673a2-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="673a2-116">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ninguna instrucción de devolución.</span><span class="sxs-lookup"><span data-stu-id="673a2-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="673a2-117">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="673a2-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="673a2-118">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="673a2-119">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="673a2-120">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="673a2-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="673a2-121">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="673a2-122">La directiva @page</span><span class="sxs-lookup"><span data-stu-id="673a2-122">The @page directive</span></span>

<span data-ttu-id="673a2-123">La directiva `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede administrar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="673a2-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="673a2-124">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="673a2-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="673a2-125">`@page` es un ejemplo de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="673a2-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="673a2-126">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="673a2-127">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="673a2-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="673a2-128">El asistente de HTML `DisplayNameFor` inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="673a2-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="673a2-129">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="673a2-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="673a2-130">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="673a2-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="673a2-131">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="673a2-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="673a2-132">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="673a2-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="673a2-133">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="673a2-134">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="673a2-135">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="673a2-136">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="673a2-136">The layout page</span></span>

<span data-ttu-id="673a2-137">Seleccione los vínculos de menú ( **RazorPagesMovie** [Película de Razor Pages], **Home** [Inicio] y **Privacy** [Privacidad]).</span><span class="sxs-lookup"><span data-stu-id="673a2-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="673a2-138">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="673a2-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="673a2-139">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="673a2-140">Abra el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="673a2-141">Las plantillas de [diseño](xref:mvc/views/layout) permiten que el diseño del contenedor HTML:</span><span class="sxs-lookup"><span data-stu-id="673a2-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="673a2-142">Esté especificado en un solo lugr.</span><span class="sxs-lookup"><span data-stu-id="673a2-142">Specified in one place.</span></span>
* <span data-ttu-id="673a2-143">Se aplique en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="673a2-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="673a2-144">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="673a2-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="673a2-145">`RenderBody` es un marcador de posición donde se muestran todas las vistas específicas de página, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="673a2-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="673a2-146">Por ejemplo, seleccione el vínculo **Privacidad** y la vista *Pages/Privacy.cshtml* se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="673a2-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="673a2-147">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="673a2-147">ViewData and layout</span></span>

<span data-ttu-id="673a2-148">Tenga en cuenta el siguiente marcado del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="673a2-149">El marcado resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="673a2-150">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="673a2-151">La clase base `PageModel` contiene una propiedad de diccionario `ViewData` que se puede usar para pasar datos a una vista.</span><span class="sxs-lookup"><span data-stu-id="673a2-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="673a2-152">Los objetos se agregan al diccionario `ViewData` con un patrón clave-valor.</span><span class="sxs-lookup"><span data-stu-id="673a2-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="673a2-153">En el ejemplo anterior, la propiedad `"Title"` se agrega al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="673a2-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="673a2-154">La propiedad `"Title"` se usa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="673a2-155">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="673a2-156">La línea `@*Markup removed for brevity.*@` es un comentario de Razor.</span><span class="sxs-lookup"><span data-stu-id="673a2-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="673a2-157">A diferencia de los comentarios HTML (`<!-- -->`), los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="673a2-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="673a2-158">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="673a2-158">Update the layout</span></span>

<span data-ttu-id="673a2-159">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="673a2-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="673a2-160">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="673a2-161">Reemplace el elemento anterior por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="673a2-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="673a2-162">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="673a2-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="673a2-163">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="673a2-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="673a2-164">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="673a2-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="673a2-165">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="673a2-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="673a2-166">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="673a2-167">Guarde los cambios y pruebe la aplicación haciendo clic en el vínculo **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="673a2-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="673a2-168">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="673a2-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="673a2-169">Pruebe los otros vínculos (**Inicio**, **RpMovie**, **Crear**, **Editar** y **Eliminar**).</span><span class="sxs-lookup"><span data-stu-id="673a2-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="673a2-170">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="673a2-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="673a2-171">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="673a2-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="673a2-172">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="673a2-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="673a2-173">Consulte este [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="673a2-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="673a2-174">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="673a2-175">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="673a2-176">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="673a2-177">Modelo de página Crear</span><span class="sxs-lookup"><span data-stu-id="673a2-177">The Create page model</span></span>

<span data-ttu-id="673a2-178">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="673a2-179">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="673a2-180">La página Crear no tiene ningún estado que inicializar, de modo que se devuelve `Page`.</span><span class="sxs-lookup"><span data-stu-id="673a2-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="673a2-181">Más adelante en el tutorial se muestra un ejemplo del estado de inicialización de `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="673a2-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="673a2-182">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="673a2-183">La propiedad `Movie` usa el atributo `[BindProperty]` para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="673a2-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="673a2-184">Cuando el formulario de creación publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="673a2-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="673a2-185">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="673a2-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="673a2-186">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="673a2-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="673a2-187">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="673a2-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="673a2-188">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="673a2-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="673a2-189">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="673a2-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="673a2-190">Si no hay ningún error de modelo, los datos se guardan y el explorador se redirige a la página Índice.</span><span class="sxs-lookup"><span data-stu-id="673a2-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="673a2-191">La página de creación de instancias de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="673a2-191">The Create Razor Page</span></span>

<span data-ttu-id="673a2-192">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="673a2-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="673a2-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="673a2-194">Visual Studio muestra las etiquetas siguientes con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="673a2-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vista de VS17 de la página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="673a2-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="673a2-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="673a2-197">Los asistentes de etiquetas siguientes se muestran en el marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="673a2-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="673a2-198">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="673a2-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="673a2-199">Visual Studio muestra las etiquetas siguientes con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="673a2-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="673a2-200">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="673a2-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="673a2-201">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="673a2-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="673a2-202">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="673a2-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="673a2-203">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="673a2-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="673a2-204">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="673a2-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="673a2-205">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `for` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="673a2-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="673a2-206">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="673a2-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="673a2-207">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="673a2-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="673a2-208">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="673a2-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="673a2-209">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="673a2-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="673a2-210">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="673a2-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="673a2-211">En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="673a2-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="673a2-212">[Vea o descargue](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) un ejemplo.</span><span class="sxs-lookup"><span data-stu-id="673a2-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="673a2-213">Páginas de creación, eliminación, detalles y edición</span><span class="sxs-lookup"><span data-stu-id="673a2-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="673a2-214">Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="673a2-215">Las instancias de Razor Pages derivan de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="673a2-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="673a2-216">Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="673a2-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="673a2-217">El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar el `RazorPagesMovieContext` a la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="673a2-218">Todas las páginas con scaffolding siguen este patrón.</span><span class="sxs-lookup"><span data-stu-id="673a2-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="673a2-219">Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="673a2-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="673a2-220">Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="673a2-221">`OnGetAsync` o `OnGet` se invocan en una instancia de Razor Pages para inicializar el estado de esa instancia.</span><span class="sxs-lookup"><span data-stu-id="673a2-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="673a2-222">En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.</span><span class="sxs-lookup"><span data-stu-id="673a2-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="673a2-223">Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ningún método de devolución.</span><span class="sxs-lookup"><span data-stu-id="673a2-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="673a2-224">Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return.</span><span class="sxs-lookup"><span data-stu-id="673a2-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="673a2-225">Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="673a2-226">Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="673a2-227">Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="673a2-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="673a2-228">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="673a2-229">La directiva de `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="673a2-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="673a2-230">`@page` debe ser la primera directiva de Razor de una página.</span><span class="sxs-lookup"><span data-stu-id="673a2-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="673a2-231">`@page` es un ejemplo de la transición a un marcado específico de Razor.</span><span class="sxs-lookup"><span data-stu-id="673a2-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="673a2-232">Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="673a2-233">Examine la expresión lambda usada en el siguiente asistente de HTML:</span><span class="sxs-lookup"><span data-stu-id="673a2-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="673a2-234">El asistente de HTML `DisplayNameFor` inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar.</span><span class="sxs-lookup"><span data-stu-id="673a2-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="673a2-235">La expresión lambda se inspecciona, no se evalúa.</span><span class="sxs-lookup"><span data-stu-id="673a2-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="673a2-236">Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos.</span><span class="sxs-lookup"><span data-stu-id="673a2-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="673a2-237">Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="673a2-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="673a2-238">La directiva @model</span><span class="sxs-lookup"><span data-stu-id="673a2-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="673a2-239">La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="673a2-240">En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="673a2-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="673a2-241">El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="673a2-242">Página de diseño</span><span class="sxs-lookup"><span data-stu-id="673a2-242">The layout page</span></span>

<span data-ttu-id="673a2-243">Seleccione los vínculos de menú ( **RazorPagesMovie** [Película de Razor Pages], **Home** [Inicio] y **Privacy** [Privacidad]).</span><span class="sxs-lookup"><span data-stu-id="673a2-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="673a2-244">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="673a2-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="673a2-245">El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="673a2-246">Abra el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="673a2-247">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="673a2-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="673a2-248">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="673a2-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="673a2-249">`RenderBody` es un marcador de posición donde se mostrarán todas las vistas específicas de página que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="673a2-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="673a2-250">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Pages/Privacy.cshtml** se representará dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="673a2-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="673a2-251">Propiedades ViewData y Layout</span><span class="sxs-lookup"><span data-stu-id="673a2-251">ViewData and layout</span></span>

<span data-ttu-id="673a2-252">Tenga en cuenta el siguiente código del archivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="673a2-253">El código resaltado anterior es un ejemplo de Razor con una transición a C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="673a2-254">Los caracteres `{` y `}` delimitan un bloque de código de C#.</span><span class="sxs-lookup"><span data-stu-id="673a2-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="673a2-255">La clase base `PageModel` tiene una propiedad de diccionario `ViewData` que se puede usar para agregar datos que quiera pasar a una vista.</span><span class="sxs-lookup"><span data-stu-id="673a2-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="673a2-256">Puede agregar objetos al diccionario `ViewData` con un patrón de clave/valor.</span><span class="sxs-lookup"><span data-stu-id="673a2-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="673a2-257">En el ejemplo anterior, se agrega la propiedad "Title" al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="673a2-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="673a2-258">La propiedad "Title" se usa en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="673a2-259">En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="673a2-260">La línea `@*Markup removed for brevity.*@` es un comentario de Razor que no aparece en el archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="673a2-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="673a2-261">A diferencia de los comentarios HTML (`<!-- -->`), los comentarios de Razor no se envían al cliente.</span><span class="sxs-lookup"><span data-stu-id="673a2-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="673a2-262">Actualizar el diseño</span><span class="sxs-lookup"><span data-stu-id="673a2-262">Update the layout</span></span>

<span data-ttu-id="673a2-263">Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="673a2-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="673a2-264">Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="673a2-265">Reemplace el elemento anterior por el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="673a2-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="673a2-266">El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="673a2-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="673a2-267">En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="673a2-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="673a2-268">El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="673a2-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="673a2-269">El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo.</span><span class="sxs-lookup"><span data-stu-id="673a2-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="673a2-270">Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="673a2-271">Guarde los cambios y pruebe la aplicación haciendo clic en el vínculo **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="673a2-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="673a2-272">Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="673a2-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="673a2-273">Pruebe los otros vínculos (**Inicio**, **RpMovie**, **Crear**, **Editar** y **Eliminar**).</span><span class="sxs-lookup"><span data-stu-id="673a2-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="673a2-274">Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.</span><span class="sxs-lookup"><span data-stu-id="673a2-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="673a2-275">Es posible que no pueda escribir comas decimales en el campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="673a2-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="673a2-276">Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="673a2-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="673a2-277">Consulte el [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obtener instrucciones sobre cómo agregar la coma decimal.</span><span class="sxs-lookup"><span data-stu-id="673a2-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="673a2-278">La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="673a2-279">El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="673a2-280">Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.</span><span class="sxs-lookup"><span data-stu-id="673a2-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="673a2-281">Modelo de página Crear</span><span class="sxs-lookup"><span data-stu-id="673a2-281">The Create page model</span></span>

<span data-ttu-id="673a2-282">Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="673a2-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="673a2-283">El método `OnGet` inicializa cualquier estado necesario para la página.</span><span class="sxs-lookup"><span data-stu-id="673a2-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="673a2-284">La página Crear no tiene ningún estado que inicializar, de modo que se devuelve `Page`.</span><span class="sxs-lookup"><span data-stu-id="673a2-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="673a2-285">Más adelante en el tutorial veremos el estado de inicialización del método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="673a2-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="673a2-286">El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="673a2-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="673a2-287">La propiedad `Movie` usa el atributo `[BindProperty]` para participar en el [enlace de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="673a2-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="673a2-288">Cuando el formulario de creación publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="673a2-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="673a2-289">El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:</span><span class="sxs-lookup"><span data-stu-id="673a2-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="673a2-290">Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados.</span><span class="sxs-lookup"><span data-stu-id="673a2-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="673a2-291">La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario.</span><span class="sxs-lookup"><span data-stu-id="673a2-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="673a2-292">Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha.</span><span class="sxs-lookup"><span data-stu-id="673a2-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="673a2-293">Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="673a2-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="673a2-294">Si no hay ningún error de modelo, los datos se guardan y el explorador se redirige a la página Índice.</span><span class="sxs-lookup"><span data-stu-id="673a2-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="673a2-295">La página de creación de instancias de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="673a2-295">The Create Razor Page</span></span>

<span data-ttu-id="673a2-296">Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="673a2-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="673a2-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="673a2-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="673a2-298">Visual Studio muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="673a2-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Vista de VS17 de la página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="673a2-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="673a2-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="673a2-301">Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="673a2-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="673a2-302">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="673a2-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="673a2-303">Visual Studio para Mac muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="673a2-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="673a2-304">El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="673a2-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="673a2-305">El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="673a2-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="673a2-306">El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="673a2-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="673a2-307">Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación.</span><span class="sxs-lookup"><span data-stu-id="673a2-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="673a2-308">La validación se trata con más detalle en un punto posterior de esta serie.</span><span class="sxs-lookup"><span data-stu-id="673a2-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="673a2-309">El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `for` para la propiedad `Title`.</span><span class="sxs-lookup"><span data-stu-id="673a2-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="673a2-310">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.</span><span class="sxs-lookup"><span data-stu-id="673a2-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="673a2-311">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="673a2-311">Additional resources</span></span>

* [<span data-ttu-id="673a2-312">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="673a2-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="673a2-313">[Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Base de datos](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="673a2-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
