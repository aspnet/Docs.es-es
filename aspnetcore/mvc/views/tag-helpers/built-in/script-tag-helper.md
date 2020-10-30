---
title: Asistente de etiquetas de script en ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra los atributos del asistente de etiquetas de script de ASP.NET Core y el papel que desempeña cada atributo al ampliar el comportamiento de la etiqueta de script de código HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053506"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="8211f-103">Asistente de etiquetas de script en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8211f-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="8211f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8211f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8211f-105">El [asistente de etiquetas de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) genera un vínculo a un archivo de script principal o de reserva.</span><span class="sxs-lookup"><span data-stu-id="8211f-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="8211f-106">Normalmente, el archivo de script principal se encuentra en una red [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="8211f-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="8211f-107">El asistente de etiquetas de script permite especificar una red CDN para el archivo de script y una reserva cuando dicha red no está disponible.</span><span class="sxs-lookup"><span data-stu-id="8211f-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="8211f-108">El asistente de etiquetas de script proporciona la ventaja de rendimiento de una red CDN con la solidez del hospedaje local.</span><span class="sxs-lookup"><span data-stu-id="8211f-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="8211f-109">En el :::no-loc(Razor)::: marcado siguiente se muestra un `script` elemento con una reserva:</span><span class="sxs-lookup"><span data-stu-id="8211f-109">The following :::no-loc(Razor)::: markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="8211f-110">No use el atributo [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) del elemento `<script>` para diferir la carga del script de CDN.</span><span class="sxs-lookup"><span data-stu-id="8211f-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="8211f-111">El Asistente de etiquetas de script representa JavaScript que ejecuta de inmediato la expresión [asp-fallback-test](#asp-fallback-test).</span><span class="sxs-lookup"><span data-stu-id="8211f-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="8211f-112">Se produce un error en la expresión si se difiere la carga del script de CDN.</span><span class="sxs-lookup"><span data-stu-id="8211f-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="8211f-113">Atributos del asistente de etiquetas de script utilizados habitualmente</span><span class="sxs-lookup"><span data-stu-id="8211f-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="8211f-114">Consulte [Asistente de etiquetas de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos los atributos, propiedades y métodos del asistente de etiquetas de script.</span><span class="sxs-lookup"><span data-stu-id="8211f-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="8211f-115">asp-fallback-test</span><span class="sxs-lookup"><span data-stu-id="8211f-115">asp-fallback-test</span></span>

<span data-ttu-id="8211f-116">Método de script definido en el script principal que se va a usar para la prueba de reserva.</span><span class="sxs-lookup"><span data-stu-id="8211f-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="8211f-117">Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="8211f-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="8211f-118">asp-fallback-src</span><span class="sxs-lookup"><span data-stu-id="8211f-118">asp-fallback-src</span></span>

<span data-ttu-id="8211f-119">Dirección URL de una etiqueta de script en la que se va a realizar la reserva en caso de error en principal.</span><span class="sxs-lookup"><span data-stu-id="8211f-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="8211f-120">Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="8211f-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8211f-121">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8211f-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
