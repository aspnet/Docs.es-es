---
title: Asistente de etiquetas parciales en ASP.NET Core
author: scottaddie
description: Conozca el asistente de etiquetas parciales en ASP.NET Core y el rol que desempeña cada uno de sus atributos a la hora de representar una vista parcial.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061436"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="1c410-103">Asistente de etiquetas parciales en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c410-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="1c410-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="1c410-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1c410-105">Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="1c410-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="1c410-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c410-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="1c410-107">Información general</span><span class="sxs-lookup"><span data-stu-id="1c410-107">Overview</span></span>

<span data-ttu-id="1c410-108">La aplicación auxiliar de etiquetas parciales se usa para representar una [vista parcial](xref:mvc/views/partial) en :::no-loc(Razor)::: páginas y aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="1c410-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="1c410-109">Tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1c410-109">Consider that it:</span></span>

* <span data-ttu-id="1c410-110">Es necesario ASP.NET Core 2.1 o una versión posterior.</span><span class="sxs-lookup"><span data-stu-id="1c410-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="1c410-111">Es una alternativa a la [sintaxis del asistente de HTML](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="1c410-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="1c410-112">Presenta la vista parcial de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="1c410-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="1c410-113">Las opciones del asistente de HTML para representar una vista parcial son estas:</span><span class="sxs-lookup"><span data-stu-id="1c410-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="1c410-114">En los ejemplos de todo este documento se usa el modelo *Product* :</span><span class="sxs-lookup"><span data-stu-id="1c410-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="1c410-115">Ahora pasaremos a ver una relación de los atributos del asistente de etiquetas parciales.</span><span class="sxs-lookup"><span data-stu-id="1c410-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="1c410-116">name</span><span class="sxs-lookup"><span data-stu-id="1c410-116">name</span></span>

<span data-ttu-id="1c410-117">El atributo `name` es necesario.</span><span class="sxs-lookup"><span data-stu-id="1c410-117">The `name` attribute is required.</span></span> <span data-ttu-id="1c410-118">Señala el nombre o la ruta de acceso de la vista parcial que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="1c410-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="1c410-119">Cuando se indica el nombre de una vista parcial, se inicia el proceso de [detección de vista](xref:mvc/views/overview#view-discovery).</span><span class="sxs-lookup"><span data-stu-id="1c410-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="1c410-120">Este proceso se omite cuando se proporciona una ruta de acceso explícita.</span><span class="sxs-lookup"><span data-stu-id="1c410-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="1c410-121">Para conocer todos los valores `name` aceptables, consulte [Detección de vistas parciales](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="1c410-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="1c410-122">En el siguiente marcado se usa una ruta de acceso explícita, lo que indica que *_ProductPartial.cshtml* debe cargarse desde la carpeta *Shared* .</span><span class="sxs-lookup"><span data-stu-id="1c410-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="1c410-123">Mediante el atributo [for](#for), se pasa un modelo a la vista parcial para el enlace.</span><span class="sxs-lookup"><span data-stu-id="1c410-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="1c410-124">para</span><span class="sxs-lookup"><span data-stu-id="1c410-124">for</span></span>

<span data-ttu-id="1c410-125">El atributo `for` asigna una [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) para que se evalúe según el modelo actual.</span><span class="sxs-lookup"><span data-stu-id="1c410-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="1c410-126">`ModelExpression` deduce la sintaxis de `@Model.`.</span><span class="sxs-lookup"><span data-stu-id="1c410-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="1c410-127">Por ejemplo, se puede usar `for="Product"` en lugar de `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="1c410-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="1c410-128">Este comportamiento predeterminado de deducción queda invalidado si se usa el símbolo `@` para definir una expresión insertada.</span><span class="sxs-lookup"><span data-stu-id="1c410-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="1c410-129">El siguiente marcado carga *_ProductPartial.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1c410-129">The following markup loads *_ProductPartial.cshtml* :</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="1c410-130">La vista parcial se enlaza a la propiedad `Product` del modelo de página asociado correspondiente:</span><span class="sxs-lookup"><span data-stu-id="1c410-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="1c410-131">model</span><span class="sxs-lookup"><span data-stu-id="1c410-131">model</span></span>

<span data-ttu-id="1c410-132">El atributo `model` asigna una instancia de modelo para pasarla a la vista parcial.</span><span class="sxs-lookup"><span data-stu-id="1c410-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="1c410-133">El atributo `model` no se puede usar con el atributo [for](#for).</span><span class="sxs-lookup"><span data-stu-id="1c410-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="1c410-134">En el siguiente código de marcado, se crea un objeto `Product` y se pasa al atributo `model` para el enlace:</span><span class="sxs-lookup"><span data-stu-id="1c410-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="1c410-135">view-data</span><span class="sxs-lookup"><span data-stu-id="1c410-135">view-data</span></span>

<span data-ttu-id="1c410-136">El atributo `view-data` asigna un [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) para pasarlo a la vista parcial.</span><span class="sxs-lookup"><span data-stu-id="1c410-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="1c410-137">El siguiente marcado hace que toda la colección ViewData esté accesible para la vista parcial:</span><span class="sxs-lookup"><span data-stu-id="1c410-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="1c410-138">En el código anterior, el valor de clave `IsNumberReadOnly` está establecido en `true` y se ha agregado a la colección ViewData.</span><span class="sxs-lookup"><span data-stu-id="1c410-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="1c410-139">Por tanto, `ViewData["IsNumberReadOnly"]` estará accesible dentro de la vista parcial siguiente:</span><span class="sxs-lookup"><span data-stu-id="1c410-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="1c410-140">En este ejemplo, el valor de `ViewData["IsNumberReadOnly"]` determina si el campo *Number* se muestra como de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="1c410-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="1c410-141">Migración desde un asistente de HTML</span><span class="sxs-lookup"><span data-stu-id="1c410-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="1c410-142">Tenga en cuenta el siguiente ejemplo del asistente de HTML asincrónico.</span><span class="sxs-lookup"><span data-stu-id="1c410-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="1c410-143">Se itera y se muestra una colección de productos.</span><span class="sxs-lookup"><span data-stu-id="1c410-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="1c410-144">Según el primer parámetro del método `PartialAsync`, se carga la vista parcial *_ProductPartial.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1c410-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="1c410-145">Se pasa una instancia del modelo `Product` a la vista parcial para el enlace.</span><span class="sxs-lookup"><span data-stu-id="1c410-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="1c410-146">El siguiente asistente de etiquetas parciales logra el mismo comportamiento de representación asincrónica que el asistente de HTML `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="1c410-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="1c410-147">El atributo `model` tiene asignada una instancia del modelo `Product` para el enlace a la vista parcial.</span><span class="sxs-lookup"><span data-stu-id="1c410-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="1c410-148">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1c410-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
