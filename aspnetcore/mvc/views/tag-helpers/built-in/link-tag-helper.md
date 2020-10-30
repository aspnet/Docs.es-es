---
title: Asistente de etiquetas de vínculo en ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra los atributos del asistente de etiquetas de vínculo de ASP.NET Core y el papel que desempeña cada atributo al ampliar el comportamiento de la etiqueta de vínculo de código HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059122"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="2e98b-103">Asistente de etiquetas de vínculo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e98b-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2e98b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e98b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2e98b-105">El [asistente de etiquetas de vínculo](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) genera un vínculo a un archivo CSS principal o de reserva.</span><span class="sxs-lookup"><span data-stu-id="2e98b-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="2e98b-106">Normalmente, el archivo CSS principal se encuentra en una red [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="2e98b-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="2e98b-107">El asistente de etiquetas de vínculo permite especificar una red CDN para el archivo CSS y una reserva cuando dicha red no está disponible.</span><span class="sxs-lookup"><span data-stu-id="2e98b-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="2e98b-108">El asistente de etiquetas de vínculo proporciona la ventaja de rendimiento de una red CDN con la solidez del hospedaje local.</span><span class="sxs-lookup"><span data-stu-id="2e98b-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="2e98b-109">En el :::no-loc(Razor)::: marcado siguiente se muestra el `head` elemento de un archivo de diseño creado con la plantilla de aplicación web de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="2e98b-109">The following :::no-loc(Razor)::: markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="2e98b-110">Lo siguiente es código HTML representado del código anterior (en un entorno que no es de desarrollo):</span><span class="sxs-lookup"><span data-stu-id="2e98b-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="2e98b-111">En el código anterior, el asistente de etiquetas de vínculo generó el elemento `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` y el siguiente código JavaScript que se usa para comprobar el archivo *bootstrap.min.css* solicitado está disponible en la red CDN.</span><span class="sxs-lookup"><span data-stu-id="2e98b-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="2e98b-112">En este caso, el archivo CSS estaba disponible, por lo que el asistente de etiquetas generó el elemento `<link />` con el archivo CSS de la red CDN.</span><span class="sxs-lookup"><span data-stu-id="2e98b-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="2e98b-113">Atributos del asistente de etiquetas de vínculo utilizados habitualmente</span><span class="sxs-lookup"><span data-stu-id="2e98b-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="2e98b-114">Consulte [Asistente de etiquetas de vínculo](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) para todos los atributos, propiedades y métodos del asistente de etiquetas de vínculo.</span><span class="sxs-lookup"><span data-stu-id="2e98b-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="2e98b-115">href</span><span class="sxs-lookup"><span data-stu-id="2e98b-115">href</span></span>

<span data-ttu-id="2e98b-116">Dirección preferida del recurso vinculado.</span><span class="sxs-lookup"><span data-stu-id="2e98b-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="2e98b-117">La dirección se pasa al código HTML generado en todos los casos.</span><span class="sxs-lookup"><span data-stu-id="2e98b-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="2e98b-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="2e98b-118">asp-fallback-href</span></span>

<span data-ttu-id="2e98b-119">La dirección URL de una hoja de estilos CSS en la que se va a realizar la reserva en caso de error en la dirección URL principal.</span><span class="sxs-lookup"><span data-stu-id="2e98b-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="2e98b-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="2e98b-120">asp-fallback-test-class</span></span>

<span data-ttu-id="2e98b-121">El nombre de clase definido en la hoja de estilos que se va a usar para la prueba de reserva.</span><span class="sxs-lookup"><span data-stu-id="2e98b-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="2e98b-122">Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="2e98b-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="2e98b-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="2e98b-123">asp-fallback-test-property</span></span>

<span data-ttu-id="2e98b-124">El nombre de la propiedad de CSS que se va a usar para la prueba de reserva.</span><span class="sxs-lookup"><span data-stu-id="2e98b-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="2e98b-125">Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="2e98b-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="2e98b-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="2e98b-126">asp-fallback-test-value</span></span>

<span data-ttu-id="2e98b-127">El valor de la propiedad de CSS que se va a usar para la prueba de reserva.</span><span class="sxs-lookup"><span data-stu-id="2e98b-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="2e98b-128">Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="2e98b-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e98b-129">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="2e98b-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
