---
title: Asistente de etiquetas delimitadoras en ASP.NET Core
author: pkellner
description: Descubra los atributos del asistente de etiquetas delimitadoras de ASP.NET Core y el papel que desempeña cada atributo al ampliar el comportamiento de la etiqueta delimitadora de código HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: d39db59b0fc273fe4193a4864f302ecd3f4ad348
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060916"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="cee62-103">Asistente de etiquetas delimitadoras en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cee62-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="cee62-104">De [Peter Kellner](https://peterkellner.net) y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="cee62-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="cee62-105">El [asistente de etiquetas delimitadoras](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) mejora la etiqueta delimitadora de código HTML estándar (`<a ... ></a>`) agregando nuevos atributos.</span><span class="sxs-lookup"><span data-stu-id="cee62-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="cee62-106">Por convención, los nombres de atributo tienen el prefijo `asp-`.</span><span class="sxs-lookup"><span data-stu-id="cee62-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="cee62-107">El valor de atributo `href` del elemento delimitador representado se determina mediante los valores de los atributos `asp-`.</span><span class="sxs-lookup"><span data-stu-id="cee62-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="cee62-108">Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="cee62-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="cee62-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cee62-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cee62-110">En los ejemplos de todo este documento se usa *SpeakerController* :</span><span class="sxs-lookup"><span data-stu-id="cee62-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="cee62-111">Atributos del asistente de etiquetas delimitadoras</span><span class="sxs-lookup"><span data-stu-id="cee62-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="cee62-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="cee62-112">asp-controller</span></span>

<span data-ttu-id="cee62-113">El atributo [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) asigna el controlador usado para generar la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="cee62-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="cee62-114">En el marcado siguiente se especifican todos los altavoces:</span><span class="sxs-lookup"><span data-stu-id="cee62-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="cee62-115">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="cee62-116">Si el atributo `asp-controller` está especificado y `asp-action` no lo está, el valor `asp-action` predeterminado es la acción del controlador asociada a la vista que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="cee62-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="cee62-117">Si se omite `asp-action` en el marcado anterior y se usa el asistente de etiquetas delimitadoras en la vista *Index* de *HomeController* ( */Home* ), el código HTML generado es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="cee62-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController* 's *Index* view ( */Home* ), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="cee62-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="cee62-118">asp-action</span></span>

<span data-ttu-id="cee62-119">El valor del atributo [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) representa el nombre de la acción del controlador incluido en el atributo `href` generado.</span><span class="sxs-lookup"><span data-stu-id="cee62-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="cee62-120">El siguiente marcado establece el valor del atributo `href` generado en la página "Speaker Evaluations":</span><span class="sxs-lookup"><span data-stu-id="cee62-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="cee62-121">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="cee62-122">Si no hay ningún atributo `asp-controller` especificado, se usará el controlador predeterminado que llama a la vista que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="cee62-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="cee62-123">Si el valor del atributo `asp-action` es `Index`, no se anexa ninguna acción a la dirección URL, lo que da lugar a la invocación de la acción `Index` predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cee62-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="cee62-124">La acción especificada (o su valor predeterminado), debe existir en el controlador al que se hace referencia en `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="cee62-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="cee62-125">asp-route-{valor}</span><span class="sxs-lookup"><span data-stu-id="cee62-125">asp-route-{value}</span></span>

<span data-ttu-id="cee62-126">El atributo [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) permite indicar un prefijo de ruta comodín.</span><span class="sxs-lookup"><span data-stu-id="cee62-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="cee62-127">Cualquier valor que ocupe el marcador de posición `{value}` se interpretará como un parámetro de ruta potencial.</span><span class="sxs-lookup"><span data-stu-id="cee62-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="cee62-128">Si no se encuentra ninguna ruta predeterminada, este prefijo de ruta se anexará al atributo `href` generado como valor y parámetro de solicitud.</span><span class="sxs-lookup"><span data-stu-id="cee62-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="cee62-129">En caso contrario, se sustituirá en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="cee62-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="cee62-130">Observe la siguiente acción del controlador:</span><span class="sxs-lookup"><span data-stu-id="cee62-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="cee62-131">Con una plantilla de ruta predeterminada definida en *Startup.Configure* :</span><span class="sxs-lookup"><span data-stu-id="cee62-131">With a default route template defined in *Startup.Configure* :</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="cee62-132">La vista de MVC usa el modelo, proporcionado por la acción, como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="cee62-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="cee62-133">Se hace coincidir el marcador de posición `{id?}` de la ruta predeterminada.</span><span class="sxs-lookup"><span data-stu-id="cee62-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="cee62-134">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="cee62-135">Supongamos que el prefijo de ruta no forma parte de la plantilla de enrutamiento coincidente, al igual que con la siguiente vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="cee62-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="cee62-136">Se genera el siguiente código HTML porque no se encontró `speakerid` en la ruta coincidente:</span><span class="sxs-lookup"><span data-stu-id="cee62-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="cee62-137">Si no se especifica `asp-controller` o `asp-action`, se sigue el mismo proceso predeterminado que en el atributo `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="cee62-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="cee62-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="cee62-138">asp-route</span></span>

<span data-ttu-id="cee62-139">El atributo [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) se usa para crear una dirección URL que se vincula directamente con una ruta con nombre.</span><span class="sxs-lookup"><span data-stu-id="cee62-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="cee62-140">Mediante [atributos de enrutamiento](xref:mvc/controllers/routing#attribute-routing) puede asignarse un nombre a una ruta tal y como se muestra en `SpeakerController` y usarse en su acción `Evaluations`:</span><span class="sxs-lookup"><span data-stu-id="cee62-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="cee62-141">En el siguiente marcado, el atributo `asp-route` hace referencia a la ruta con nombre:</span><span class="sxs-lookup"><span data-stu-id="cee62-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="cee62-142">El asistente de etiquetas delimitadoras genera una ruta directamente a esa acción de controlador mediante la dirección URL */Speaker/Evaluations* .</span><span class="sxs-lookup"><span data-stu-id="cee62-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations* .</span></span> <span data-ttu-id="cee62-143">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="cee62-144">Si además de `asp-route` se especifica `asp-controller` o `asp-action`, la ruta generada puede no ser la esperada.</span><span class="sxs-lookup"><span data-stu-id="cee62-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="cee62-145">Para evitar un conflicto de ruta, no se debe usar `asp-route` con los atributos `asp-controller` y `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="cee62-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="cee62-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="cee62-146">asp-all-route-data</span></span>

<span data-ttu-id="cee62-147">El atributo [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) permite crear un diccionario de pares clave-valor.</span><span class="sxs-lookup"><span data-stu-id="cee62-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="cee62-148">La clave es el nombre del parámetro, mientras que el valor es el valor del parámetro.</span><span class="sxs-lookup"><span data-stu-id="cee62-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="cee62-149">En el ejemplo siguiente, se inicializa un diccionario y se pasa a una Razor vista.</span><span class="sxs-lookup"><span data-stu-id="cee62-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="cee62-150">Los datos también se podrían pasar con el modelo.</span><span class="sxs-lookup"><span data-stu-id="cee62-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="cee62-151">El código anterior genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="cee62-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="cee62-152">Se acopla el diccionario `asp-all-route-data` para generar una cadena de consulta que cumpla los requisitos de la acción `Evaluations` sobrecargada:</span><span class="sxs-lookup"><span data-stu-id="cee62-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="cee62-153">Si alguna de las claves del diccionario coincide con los parámetros de ruta, esos valores se sustituirán en la ruta según corresponda.</span><span class="sxs-lookup"><span data-stu-id="cee62-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="cee62-154">Los demás valores no coincidentes se generarán como parámetros de solicitud.</span><span class="sxs-lookup"><span data-stu-id="cee62-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="cee62-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="cee62-155">asp-fragment</span></span>

<span data-ttu-id="cee62-156">El atributo [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) define un fragmento de dirección URL que se anexará a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="cee62-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="cee62-157">El asistente de etiquetas delimitadoras agrega el carácter de almohadilla (#).</span><span class="sxs-lookup"><span data-stu-id="cee62-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="cee62-158">Observe el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="cee62-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="cee62-159">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="cee62-160">Las etiquetas hash son útiles al crear aplicaciones del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="cee62-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="cee62-161">Por ejemplo, se pueden usar para el marcado y la búsqueda en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cee62-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="cee62-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="cee62-162">asp-area</span></span>

<span data-ttu-id="cee62-163">El atributo [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) establece el nombre de área que se usa para establecer la ruta adecuada.</span><span class="sxs-lookup"><span data-stu-id="cee62-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="cee62-164">En el siguiente ejemplo se muestra cómo el atributo `asp-area` provoca una reasignación de rutas.</span><span class="sxs-lookup"><span data-stu-id="cee62-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-no-locrazor-pages"></a><span data-ttu-id="cee62-165">Uso en Razor páginas</span><span class="sxs-lookup"><span data-stu-id="cee62-165">Usage in Razor Pages</span></span>

<span data-ttu-id="cee62-166">Razor Las áreas de páginas se admiten en ASP.NET Core 2,1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="cee62-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="cee62-167">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="cee62-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="cee62-168">**{Nombre del proyecto}**</span><span class="sxs-lookup"><span data-stu-id="cee62-168">**{Project name}**</span></span>
  * <span data-ttu-id="cee62-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="cee62-169">**wwwroot**</span></span>
  * <span data-ttu-id="cee62-170">**Áreas**</span><span class="sxs-lookup"><span data-stu-id="cee62-170">**Areas**</span></span>
    * <span data-ttu-id="cee62-171">**Sesiones**</span><span class="sxs-lookup"><span data-stu-id="cee62-171">**Sessions**</span></span>
      * <span data-ttu-id="cee62-172">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="cee62-172">**Pages**</span></span>
        * <span data-ttu-id="cee62-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cee62-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="cee62-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cee62-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="cee62-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="cee62-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="cee62-176">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="cee62-176">**Pages**</span></span>

<span data-ttu-id="cee62-177">El marcado para hacer referencia a la página de *Índice* del área de *sesiones* Razor es:</span><span class="sxs-lookup"><span data-stu-id="cee62-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="cee62-178">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="cee62-179">Para admitir áreas en una Razor aplicación de páginas, realice una de las siguientes acciones en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cee62-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="cee62-180">Establezca la [versión de compatibilidad](xref:mvc/compatibility-version) en 2.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="cee62-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="cee62-181">Establezca la propiedad [ Razor PagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) en `true` :</span><span class="sxs-lookup"><span data-stu-id="cee62-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="cee62-182">Uso en MVC</span><span class="sxs-lookup"><span data-stu-id="cee62-182">Usage in MVC</span></span>

<span data-ttu-id="cee62-183">Tenga en cuenta la siguiente jerarquía de directorios:</span><span class="sxs-lookup"><span data-stu-id="cee62-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="cee62-184">**{Nombre del proyecto}**</span><span class="sxs-lookup"><span data-stu-id="cee62-184">**{Project name}**</span></span>
  * <span data-ttu-id="cee62-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="cee62-185">**wwwroot**</span></span>
  * <span data-ttu-id="cee62-186">**Áreas**</span><span class="sxs-lookup"><span data-stu-id="cee62-186">**Areas**</span></span>
    * <span data-ttu-id="cee62-187">**Blogs**</span><span class="sxs-lookup"><span data-stu-id="cee62-187">**Blogs**</span></span>
      * <span data-ttu-id="cee62-188">**Controladores**</span><span class="sxs-lookup"><span data-stu-id="cee62-188">**Controllers**</span></span>
        * <span data-ttu-id="cee62-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="cee62-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="cee62-190">**Vistas**</span><span class="sxs-lookup"><span data-stu-id="cee62-190">**Views**</span></span>
        * <span data-ttu-id="cee62-191">**Página principal**</span><span class="sxs-lookup"><span data-stu-id="cee62-191">**Home**</span></span>
          * <span data-ttu-id="cee62-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cee62-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="cee62-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cee62-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="cee62-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cee62-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="cee62-195">**Controladores**</span><span class="sxs-lookup"><span data-stu-id="cee62-195">**Controllers**</span></span>

<span data-ttu-id="cee62-196">Al establecer `asp-area` en "Blogs", el directorio *Areas/Blogs* se prefija en las rutas de los controladores y vistas asociados de esta etiqueta delimitadora.</span><span class="sxs-lookup"><span data-stu-id="cee62-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="cee62-197">El marcado para hacer referencia a la vista *AboutBlog* es:</span><span class="sxs-lookup"><span data-stu-id="cee62-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="cee62-198">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="cee62-199">Para admitir áreas en una aplicación de MVC, la plantilla de ruta debe incluir una referencia al área, en el caso de que exista.</span><span class="sxs-lookup"><span data-stu-id="cee62-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="cee62-200">Esta plantilla se representa mediante el segundo parámetro de la llamada de método `routes.MapRoute` en *Startup.Configure* :</span><span class="sxs-lookup"><span data-stu-id="cee62-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure* :</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="cee62-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="cee62-201">asp-protocol</span></span>

<span data-ttu-id="cee62-202">El atributo [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) sirve para especificar un protocolo (por ejemplo, `https`) en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="cee62-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="cee62-203">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cee62-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="cee62-204">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="cee62-205">El nombre de host en el ejemplo es localhost.</span><span class="sxs-lookup"><span data-stu-id="cee62-205">The host name in the example is localhost.</span></span> <span data-ttu-id="cee62-206">El asistente de etiquetas delimitadoras utiliza el dominio público del sitio web al generar la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="cee62-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="cee62-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="cee62-207">asp-host</span></span>

<span data-ttu-id="cee62-208">El atributo [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) sirve para especificar un nombre de host en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="cee62-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="cee62-209">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cee62-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="cee62-210">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="cee62-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="cee62-211">asp-page</span></span>

<span data-ttu-id="cee62-212">El atributo [asp-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) se usa con Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="cee62-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="cee62-213">Úselo para establecer el valor del atributo `href` de una etiqueta delimitadora en una página específica.</span><span class="sxs-lookup"><span data-stu-id="cee62-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="cee62-214">La dirección URL se crea al prefijar el nombre de la página con una barra diagonal ("/").</span><span class="sxs-lookup"><span data-stu-id="cee62-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="cee62-215">El siguiente ejemplo señala a la página de asistentes Razor :</span><span class="sxs-lookup"><span data-stu-id="cee62-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="cee62-216">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="cee62-217">El atributo `asp-page` es mutuamente excluyente con los atributos `asp-route`, `asp-controller` y `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="cee62-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="cee62-218">Pero se puede usar `asp-page` con `asp-route-{value}` para controlar el enrutamiento, como se muestra en el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="cee62-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="cee62-219">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="cee62-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="cee62-220">asp-page-handler</span></span>

<span data-ttu-id="cee62-221">El atributo [asp-Page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) se usa con Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="cee62-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="cee62-222">Está diseñado para crear un vínculo con controladores de página específicos.</span><span class="sxs-lookup"><span data-stu-id="cee62-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="cee62-223">Observe el siguiente controlador de página:</span><span class="sxs-lookup"><span data-stu-id="cee62-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="cee62-224">El marcado asociado del modelo de página se vincula con el controlador de página `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="cee62-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="cee62-225">Tenga en cuenta que el prefijo `On<Verb>` del nombre de método del controlador de página se omite en el valor del atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="cee62-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="cee62-226">Cuando el método es asincrónico, también se omite el sufijo `Async`.</span><span class="sxs-lookup"><span data-stu-id="cee62-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="cee62-227">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="cee62-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="cee62-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cee62-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
