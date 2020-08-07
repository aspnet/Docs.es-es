---
title: Asistentes de etiquetas en formularios de ASP.NET Core
author: rick-anderson
description: En este tema se describen los asistentes de etiquetas que se usan en los formularios.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/working-with-forms
ms.openlocfilehash: 833939417c4973c61eca4d41aaeabe7856680190
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913858"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="c216f-103">Asistentes de etiquetas en formularios de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c216f-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="c216f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) y [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="c216f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="c216f-105">En este documento se explica cómo trabajar con formularios y se detallan los elementos HTML que se usan habitualmente en un formulario.</span><span class="sxs-lookup"><span data-stu-id="c216f-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="c216f-106">El elemento HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) proporciona el mecanismo principal que las aplicaciones web usan a la hora de devolver datos al servidor.</span><span class="sxs-lookup"><span data-stu-id="c216f-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="c216f-107">La mayor parte de este documento se centra en describir los [asistentes de etiquetas](tag-helpers/intro.md) y cómo pueden servir para crear formularios HTML eficaces de manera productiva.</span><span class="sxs-lookup"><span data-stu-id="c216f-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="c216f-108">Se recomienda leer [Introduction to Tag Helpers](tag-helpers/intro.md) (Introducción a los asistentes de etiquetas) antes de este documento.</span><span class="sxs-lookup"><span data-stu-id="c216f-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="c216f-109">En muchos casos, los asistentes de HTML proporcionan un método alternativo para un asistente de etiquetas específico, pero es importante tener en cuenta que los asistentes de etiquetas no reemplazan a los asistentes de HTML y que no hay un asistente de etiquetas para cada asistente de HTML.</span><span class="sxs-lookup"><span data-stu-id="c216f-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="c216f-110">Si existe una alternativa del asistente de HTML, se mencionará aquí.</span><span class="sxs-lookup"><span data-stu-id="c216f-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="c216f-111">Asistente de etiquetas de formulario (Form)</span><span class="sxs-lookup"><span data-stu-id="c216f-111">The Form Tag Helper</span></span>

<span data-ttu-id="c216f-112">Aplicación auxiliar de etiquetas de [formulario](https://www.w3.org/TR/html401/interact/forms.html) :</span><span class="sxs-lookup"><span data-stu-id="c216f-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="c216f-113">Genera el [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` valor de atributo HTML para una acción del controlador de MVC o una ruta con nombre.</span><span class="sxs-lookup"><span data-stu-id="c216f-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="c216f-114">Genera un [token comprobación de solicitudes](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto que impide que se falsifiquen solicitudes entre sitios (cuando se usa con el atributo `[ValidateAntiForgeryToken]` en el método de acción HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="c216f-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="c216f-115">Proporciona el atributo `asp-route-<Parameter Name>`, donde `<Parameter Name>` se agrega a los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="c216f-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="c216f-116">Los parámetros `routeValues` de `Html.BeginForm` y `Html.BeginRouteForm` proporcionan una funcionalidad similar.</span><span class="sxs-lookup"><span data-stu-id="c216f-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="c216f-117">Tiene `Html.BeginForm` y `Html.BeginRouteForm` como alternativa del asistente de HTML.</span><span class="sxs-lookup"><span data-stu-id="c216f-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="c216f-118">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-118">Sample:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="c216f-119">El asistente de etiquetas Form anterior genera el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="c216f-120">El tiempo de ejecución MVC genera el valor de atributo `action` de los atributos del asistente de etiquetas Form `asp-controller` y `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="c216f-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="c216f-121">El asistente de etiquetas Form genera también un [token de comprobación de solicitudes](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto que impide que se falsifiquen solicitudes entre sitios (cuando se usa con el atributo `[ValidateAntiForgeryToken]` en el método de acción HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="c216f-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="c216f-122">Proteger un elemento HTML Form puro de la falsificación de solicitudes entre sitios no es tarea fácil, y el asistente de etiquetas Form presta este servicio.</span><span class="sxs-lookup"><span data-stu-id="c216f-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="c216f-123">Uso de una ruta con nombre</span><span class="sxs-lookup"><span data-stu-id="c216f-123">Using a named route</span></span>

<span data-ttu-id="c216f-124">El atributo del asistente de etiquetas `asp-route` puede generar también el marcado del atributo HTML `action`.</span><span class="sxs-lookup"><span data-stu-id="c216f-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="c216f-125">Una aplicación con una [ruta](../../fundamentals/routing.md) denominada `register` podría usar el siguiente marcado para la página de registro:</span><span class="sxs-lookup"><span data-stu-id="c216f-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="c216f-126">Muchas de las vistas de la carpeta Views/Account (que se genera cuando se crea una aplicación web con Cuentas de usuario individuales) contienen el atributoasp-route-returnurl:</span><span class="sxs-lookup"><span data-stu-id="c216f-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="c216f-127">Con las plantillas integradas, `returnUrl` se rellena automáticamente solo cuando alguien intenta obtener acceso a un recurso autorizado, pero no se ha autenticado o no tiene autorización.</span><span class="sxs-lookup"><span data-stu-id="c216f-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="c216f-128">Si se intenta realizar un acceso no autorizado, el middleware de seguridad redirige a la página de inicio de sesión con `returnUrl` configurado.</span><span class="sxs-lookup"><span data-stu-id="c216f-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="c216f-129">Asistente de etiquetas de acción de formulario</span><span class="sxs-lookup"><span data-stu-id="c216f-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="c216f-130">El asistente de etiquetas de acción de formulario genera el atributo `formaction` en la etiqueta `<button ...>` o `<input type="image" ...>` generadas.</span><span class="sxs-lookup"><span data-stu-id="c216f-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="c216f-131">El atributo `formaction` controla el lugar donde un formulario envía sus datos.</span><span class="sxs-lookup"><span data-stu-id="c216f-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="c216f-132">Enlaza a [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elementos de tipo `image` y [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elementos.</span><span class="sxs-lookup"><span data-stu-id="c216f-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="c216f-133">El asistente de etiquetas de acción de formulario permite el uso de varios atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` para controlar qué vínculo `formaction` se genera para el elemento correspondiente.</span><span class="sxs-lookup"><span data-stu-id="c216f-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="c216f-134">Atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) admitidos para controlar el valor de `formaction`:</span><span class="sxs-lookup"><span data-stu-id="c216f-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="c216f-135">Atributo</span><span class="sxs-lookup"><span data-stu-id="c216f-135">Attribute</span></span>|<span data-ttu-id="c216f-136">Descripción</span><span class="sxs-lookup"><span data-stu-id="c216f-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="c216f-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="c216f-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="c216f-138">El nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="c216f-138">The name of the controller.</span></span>|
|[<span data-ttu-id="c216f-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="c216f-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="c216f-140">El nombre del método de acción.</span><span class="sxs-lookup"><span data-stu-id="c216f-140">The name of the action method.</span></span>|
|[<span data-ttu-id="c216f-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="c216f-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="c216f-142">El nombre del área.</span><span class="sxs-lookup"><span data-stu-id="c216f-142">The name of the area.</span></span>|
|[<span data-ttu-id="c216f-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="c216f-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="c216f-144">Nombre de la Razor página.</span><span class="sxs-lookup"><span data-stu-id="c216f-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="c216f-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="c216f-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="c216f-146">Nombre del controlador de la Razor página.</span><span class="sxs-lookup"><span data-stu-id="c216f-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="c216f-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="c216f-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="c216f-148">El nombre de la ruta.</span><span class="sxs-lookup"><span data-stu-id="c216f-148">The name of the route.</span></span>|
|[<span data-ttu-id="c216f-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="c216f-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="c216f-150">Un valor único de ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="c216f-150">A single URL route value.</span></span> <span data-ttu-id="c216f-151">Por ejemplo: `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="c216f-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="c216f-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="c216f-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="c216f-153">Todos los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="c216f-153">All route values.</span></span>|
|[<span data-ttu-id="c216f-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="c216f-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="c216f-155">El fragmento de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="c216f-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="c216f-156">Ejemplo de envío al controlador</span><span class="sxs-lookup"><span data-stu-id="c216f-156">Submit to controller example</span></span>

<span data-ttu-id="c216f-157">El marcado siguiente envía el formulario a la acción `Index` de `HomeController` cuando se selecciona el elemento input o button:</span><span class="sxs-lookup"><span data-stu-id="c216f-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="c216f-158">El marcado anterior genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="c216f-159">Ejemplo de envío a la página</span><span class="sxs-lookup"><span data-stu-id="c216f-159">Submit to page example</span></span>

<span data-ttu-id="c216f-160">El marcado siguiente envía el formulario a la `About` Razor página:</span><span class="sxs-lookup"><span data-stu-id="c216f-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="c216f-161">El marcado anterior genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="c216f-162">Ejemplo de envío a la ruta</span><span class="sxs-lookup"><span data-stu-id="c216f-162">Submit to route example</span></span>

<span data-ttu-id="c216f-163">Tenga en cuenta el punto de conexión `/Home/Test`:</span><span class="sxs-lookup"><span data-stu-id="c216f-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="c216f-164">El marcado siguiente envía el formulario al punto de conexión `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="c216f-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="c216f-165">El marcado anterior genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="c216f-166">Asistente de etiquetas de entrada (Input)</span><span class="sxs-lookup"><span data-stu-id="c216f-166">The Input Tag Helper</span></span>

<span data-ttu-id="c216f-167">La aplicación auxiliar de etiquetas de entrada enlaza un [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elemento HTML a una expresión de modelo en la vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="c216f-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="c216f-168">Sintaxis:</span><span class="sxs-lookup"><span data-stu-id="c216f-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="c216f-169">El asistente de etiquetas Input hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c216f-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="c216f-170">Genera los atributos HTML  y  del nombre de expresión especificado en el atributo.</span><span class="sxs-lookup"><span data-stu-id="c216f-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="c216f-171">`asp-for="Property1.Property2"` equivale a `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="c216f-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="c216f-172">El nombre de una expresión es lo que se usa para el valor de atributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="c216f-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="c216f-173">Vea la sección [Nombres de expresión](#expression-names) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c216f-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="c216f-174">Establece el valor de atributo HTML `type` según los atributos de tipo de modelo y de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a la propiedad de modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="c216f-175">No sobrescribirá el valor de atributo HTML `type` cuando se especifique uno.</span><span class="sxs-lookup"><span data-stu-id="c216f-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="c216f-176">Genera atributos de validación [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) a partir de los atributos de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a las propiedades de modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="c216f-177">Tiene características del asistente de HTML que se superponen a `Html.TextBoxFor` y `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="c216f-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="c216f-178">Vea la sección **Alternativas del asistente de HTML al asistente de etiquetas Input** para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c216f-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="c216f-179">Permite establecer tipado fuerte.</span><span class="sxs-lookup"><span data-stu-id="c216f-179">Provides strong typing.</span></span> <span data-ttu-id="c216f-180">Si el nombre de la propiedad cambia y no se actualiza el asistente de etiquetas, aparecerá un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="c216f-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

<span data-ttu-id="c216f-181">El asistente de etiquetas `Input` establece el atributo HTML `type` en función del tipo .NET.</span><span class="sxs-lookup"><span data-stu-id="c216f-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="c216f-182">En la siguiente tabla se enumeran algunos tipos .NET habituales y el tipo HTML generado correspondiente (no incluimos aquí todos los tipos .NET).</span><span class="sxs-lookup"><span data-stu-id="c216f-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="c216f-183">Tipo de .NET</span><span class="sxs-lookup"><span data-stu-id="c216f-183">.NET type</span></span>|<span data-ttu-id="c216f-184">Tipo de entrada</span><span class="sxs-lookup"><span data-stu-id="c216f-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c216f-185">Bool</span><span class="sxs-lookup"><span data-stu-id="c216f-185">Bool</span></span>|<span data-ttu-id="c216f-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="c216f-186">type="checkbox"</span></span>|
|<span data-ttu-id="c216f-187">String</span><span class="sxs-lookup"><span data-stu-id="c216f-187">String</span></span>|<span data-ttu-id="c216f-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="c216f-188">type="text"</span></span>|
|<span data-ttu-id="c216f-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="c216f-189">DateTime</span></span>|<span data-ttu-id="c216f-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="c216f-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="c216f-191">Byte</span><span class="sxs-lookup"><span data-stu-id="c216f-191">Byte</span></span>|<span data-ttu-id="c216f-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="c216f-192">type="number"</span></span>|
|<span data-ttu-id="c216f-193">Int</span><span class="sxs-lookup"><span data-stu-id="c216f-193">Int</span></span>|<span data-ttu-id="c216f-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="c216f-194">type="number"</span></span>|
|<span data-ttu-id="c216f-195">Single, Double</span><span class="sxs-lookup"><span data-stu-id="c216f-195">Single, Double</span></span>|<span data-ttu-id="c216f-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="c216f-196">type="number"</span></span>|

<span data-ttu-id="c216f-197">En la siguiente tabla se muestran algunos atributos de [anotación de datos](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) comunes que el asistente de etiquetas Input asignará a tipos de entrada concretos (no incluimos aquí todos los atributo de validación):</span><span class="sxs-lookup"><span data-stu-id="c216f-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="c216f-198">Atributo</span><span class="sxs-lookup"><span data-stu-id="c216f-198">Attribute</span></span>|<span data-ttu-id="c216f-199">Tipo de entrada</span><span class="sxs-lookup"><span data-stu-id="c216f-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c216f-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="c216f-200">[EmailAddress]</span></span>|<span data-ttu-id="c216f-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="c216f-201">type="email"</span></span>|
|<span data-ttu-id="c216f-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="c216f-202">[Url]</span></span>|<span data-ttu-id="c216f-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="c216f-203">type="url"</span></span>|
|<span data-ttu-id="c216f-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="c216f-204">[HiddenInput]</span></span>|<span data-ttu-id="c216f-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="c216f-205">type="hidden"</span></span>|
|<span data-ttu-id="c216f-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="c216f-206">[Phone]</span></span>|<span data-ttu-id="c216f-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="c216f-207">type="tel"</span></span>|
|<span data-ttu-id="c216f-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="c216f-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="c216f-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="c216f-209">type="password"</span></span>|
|<span data-ttu-id="c216f-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="c216f-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="c216f-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="c216f-211">type="date"</span></span>|
|<span data-ttu-id="c216f-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="c216f-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="c216f-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="c216f-213">type="time"</span></span>|

<span data-ttu-id="c216f-214">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="c216f-215">El código anterior genera el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="c216f-216">Las anotaciones de datos que se aplican a las propiedades `Email` y `Password` generan metadatos en el modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="c216f-217">El asistente de etiquetas de entrada usa esos metadatos del modelo y genera atributos [HTML5 ](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (consulte [Validación del modelo](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="c216f-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="c216f-218">Estos atributos describen los validadores que se van a adjuntar a los campos de entrada,</span><span class="sxs-lookup"><span data-stu-id="c216f-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="c216f-219">lo que proporciona HTML5 discreto y validación de [jQuery](https://jquery.com/).</span><span class="sxs-lookup"><span data-stu-id="c216f-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="c216f-220">Los atributos discretos tienen el formato `data-val-rule="Error Message"` , donde Rule es el nombre de la regla de validación (por ejemplo,,, `data-val-required` `data-val-email` `data-val-maxlength` , etc.). Si se proporciona un mensaje de error en el atributo, se muestra como el valor del `data-val-rule` atributo.</span><span class="sxs-lookup"><span data-stu-id="c216f-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="c216f-221">También hay atributos con el formato `data-val-ruleName-argumentName="argumentValue"` que aportan más información sobre la regla, por ejemplo, `data-val-maxlength-max="1024"`.</span><span class="sxs-lookup"><span data-stu-id="c216f-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="c216f-222">Alternativas del asistente de HTML al asistente de etiquetas Input</span><span class="sxs-lookup"><span data-stu-id="c216f-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="c216f-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` y `Html.EditorFor` tienen características que se superponen al asistente de etiquetas Input.</span><span class="sxs-lookup"><span data-stu-id="c216f-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="c216f-224">El asistente de etiquetas Input establecerá automáticamente el atributo `type`, cosa que no ocurrirá con `Html.TextBox` ni `Html.TextBoxFor`.</span><span class="sxs-lookup"><span data-stu-id="c216f-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="c216f-225">`Html.Editor` y `Html.EditorFor` controlan colecciones, objetos complejos y plantillas, pero el asistente de etiquetas Input no.</span><span class="sxs-lookup"><span data-stu-id="c216f-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="c216f-226">El asistente de etiquetas Input, `Html.EditorFor` y `Html.TextBoxFor` están fuertemente tipados (usan expresiones lambda), pero `Html.TextBox` y `Html.Editor` no (usan nombres de expresión).</span><span class="sxs-lookup"><span data-stu-id="c216f-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="c216f-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="c216f-227">HtmlAttributes</span></span>

<span data-ttu-id="c216f-228">`@Html.Editor()` y `@Html.EditorFor()` usan una entrada `ViewDataDictionary` especial denominada `htmlAttributes` al ejecutar sus plantillas predeterminadas.</span><span class="sxs-lookup"><span data-stu-id="c216f-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="c216f-229">Si lo desea, este comportamiento se puede enriquecer con parámetros `additionalViewData`.</span><span class="sxs-lookup"><span data-stu-id="c216f-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="c216f-230">En la clave "htmlAttributes" se distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c216f-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="c216f-231">La clave "htmlAttributes" se controla de forma similar al objeto `htmlAttributes` pasado a asistentes de etiquetas Input como `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="c216f-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="c216f-232">Nombres de expresión</span><span class="sxs-lookup"><span data-stu-id="c216f-232">Expression names</span></span>

<span data-ttu-id="c216f-233">El valor del atributo `asp-for` es una `ModelExpression` y la parte de la derecha de una expresión lambda.</span><span class="sxs-lookup"><span data-stu-id="c216f-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="c216f-234">Por tanto, `asp-for="Property1"` se convierte en `m => m.Property1` en el código generado, motivo por el que no es necesario incluir el prefijo `Model`.</span><span class="sxs-lookup"><span data-stu-id="c216f-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="c216f-235">Puede usar el carácter "\@" para iniciar una expresión insertada y moverla antes de `m.`:</span><span class="sxs-lookup"><span data-stu-id="c216f-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="c216f-236">Se genera el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="c216f-237">Con las propiedades de colección, `asp-for="CollectionProperty[23].Member"` genera el mismo nombre que `asp-for="CollectionProperty[i].Member"` si `i` tiene el valor `23`.</span><span class="sxs-lookup"><span data-stu-id="c216f-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="c216f-238">Cuando ASP.NET Core MVC calcula el valor de `ModelExpression`, inspecciona varios orígenes, `ModelState` incluido.</span><span class="sxs-lookup"><span data-stu-id="c216f-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="c216f-239">Fíjese en `<input type="text" asp-for="@Name">`.</span><span class="sxs-lookup"><span data-stu-id="c216f-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="c216f-240">El atributo `value` calculado es el primer valor distinto de null de:</span><span class="sxs-lookup"><span data-stu-id="c216f-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="c216f-241">La entrada `ModelState` con la clave "Name".</span><span class="sxs-lookup"><span data-stu-id="c216f-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="c216f-242">El resultado de la expresión `Model.Name`.</span><span class="sxs-lookup"><span data-stu-id="c216f-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="c216f-243">Navegar a las propiedades secundarias</span><span class="sxs-lookup"><span data-stu-id="c216f-243">Navigating child properties</span></span>

<span data-ttu-id="c216f-244">También se puede navegar a las propiedades secundarias a través de la ruta de acceso de propiedades del modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="c216f-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="c216f-245">Pensemos en una clase de modelo más compleja que contiene una propiedad secundaria `Address`.</span><span class="sxs-lookup"><span data-stu-id="c216f-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="c216f-246">En la vista, enlazamos a `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="c216f-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="c216f-247">Se genera el siguiente código HTML para `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="c216f-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="c216f-248">Colecciones y nombres de expresión</span><span class="sxs-lookup"><span data-stu-id="c216f-248">Expression names and Collections</span></span>

<span data-ttu-id="c216f-249">Como ejemplo, un modelo que contiene una matriz de `Colors`:</span><span class="sxs-lookup"><span data-stu-id="c216f-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="c216f-250">El método de acción:</span><span class="sxs-lookup"><span data-stu-id="c216f-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="c216f-251">A continuación Razor se muestra cómo se obtiene acceso a un `Color` elemento específico:</span><span class="sxs-lookup"><span data-stu-id="c216f-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="c216f-252">La plantilla *Views/Shared/EditorTemplates/String.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c216f-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="c216f-253">Ejemplo en el que se usa `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="c216f-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="c216f-254">A continuación Razor se muestra cómo recorrer en iteración una colección:</span><span class="sxs-lookup"><span data-stu-id="c216f-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="c216f-255">La plantilla *Views/Shared/EditorTemplates/ToDoItem.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c216f-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="c216f-256">Si es posible, debe usarse `foreach` si el valor se va a utilizar en un contexto equivalente a `asp-for` o `Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="c216f-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="c216f-257">En general, `for` es mejor que `foreach` (si el escenario lo permite), ya que no necesita asignar ningún enumerador; sin embargo, la evaluación de un indizador en una expresión LINQ puede resultar caro y, por tanto, se debe minimizar.</span><span class="sxs-lookup"><span data-stu-id="c216f-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="c216f-258">El código de ejemplo comentado anterior muestra cómo reemplazaríamos la expresión lambda por el operador `@` para tener acceso a cada elemento `ToDoItem` de la lista.</span><span class="sxs-lookup"><span data-stu-id="c216f-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="c216f-259">Asistente de etiquetas de área de texto (Textarea)</span><span class="sxs-lookup"><span data-stu-id="c216f-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="c216f-260">El asistente de etiquetas `Textarea Tag Helper` es similar al asistente de etiquetas Input.</span><span class="sxs-lookup"><span data-stu-id="c216f-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="c216f-261">Genera los `id` atributos y y `name` los atributos de validación de datos del modelo para un [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) elemento.</span><span class="sxs-lookup"><span data-stu-id="c216f-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="c216f-262">Permite establecer tipado fuerte.</span><span class="sxs-lookup"><span data-stu-id="c216f-262">Provides strong typing.</span></span>

* <span data-ttu-id="c216f-263">Alternativa del asistente de HTML: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="c216f-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="c216f-264">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="c216f-265">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="c216f-266">Asistente de etiquetas Label</span><span class="sxs-lookup"><span data-stu-id="c216f-266">The Label Tag Helper</span></span>

* <span data-ttu-id="c216f-267">Genera el título y el `for` atributo de la etiqueta en un [\<label>](https://www.w3.org/wiki/HTML/Elements/label) elemento para un nombre de expresión.</span><span class="sxs-lookup"><span data-stu-id="c216f-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="c216f-268">Alternativa del asistente de HTML: `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="c216f-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="c216f-269">`Label Tag Helper` proporciona las siguientes ventajas con respecto a un elemento HTML Label puro:</span><span class="sxs-lookup"><span data-stu-id="c216f-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="c216f-270">Obtendrá automáticamente el valor de la etiqueta descriptiva del atributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="c216f-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="c216f-271">El nombre para mostrar que se busca puede cambiar con el tiempo y la combinación del atributo `Display`, y el asistente de etiquetas Label aplicará el elemento `Display` en cualquier lugar donde se use.</span><span class="sxs-lookup"><span data-stu-id="c216f-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="c216f-272">El código fuente contiene menos marcado.</span><span class="sxs-lookup"><span data-stu-id="c216f-272">Less markup in source code</span></span>

* <span data-ttu-id="c216f-273">Permite establecer tipado fuerte con la propiedad de modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-273">Strong typing with the model property.</span></span>

<span data-ttu-id="c216f-274">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="c216f-275">Se genera el siguiente código HTML para el elemento `<label>`:</span><span class="sxs-lookup"><span data-stu-id="c216f-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="c216f-276">El asistente de etiquetas Label genera el valor de atributo `for` de "Email", que es el identificador asociado al elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c216f-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="c216f-277">Los asistentes de etiquetas generan elementos `id` y `for` coherentes para que se puedan asociar correctamente.</span><span class="sxs-lookup"><span data-stu-id="c216f-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="c216f-278">El título de este ejemplo proviene del atributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="c216f-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="c216f-279">Si el modelo no contuviera un atributo `Display`, el título sería el nombre de propiedad de la expresión.</span><span class="sxs-lookup"><span data-stu-id="c216f-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="c216f-280">Asistentes de etiquetas de validación</span><span class="sxs-lookup"><span data-stu-id="c216f-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="c216f-281">Hay dos asistentes de etiquetas de validación.</span><span class="sxs-lookup"><span data-stu-id="c216f-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="c216f-282">`Validation Message Tag Helper` (que muestra un mensaje de validación relativo a una única propiedad del modelo) y `Validation Summary Tag Helper` (que muestra un resumen de los errores de validación).</span><span class="sxs-lookup"><span data-stu-id="c216f-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="c216f-283">`Input Tag Helper` agrega atributos de validación del lado cliente HTML5 a los elementos de entrada en función de los atributos de anotación de datos de las clases del modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="c216f-284">La validación también se realiza en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c216f-284">Validation is also performed on the server.</span></span> <span data-ttu-id="c216f-285">El asistente de etiquetas de validación muestra estos mensajes de error cuando se produce un error de validación.</span><span class="sxs-lookup"><span data-stu-id="c216f-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="c216f-286">Asistente de etiquetas de mensaje de validación</span><span class="sxs-lookup"><span data-stu-id="c216f-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="c216f-287">Agrega el atributo [HTML5 ](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` al elemento [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), que adjunta los mensajes de error de validación en el campo de entrada de la propiedad de modelo especificada.</span><span class="sxs-lookup"><span data-stu-id="c216f-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="c216f-288">Cuando se produce un error de validación en el lado cliente, [jQuery](https://jquery.com/) muestra el mensaje de error en el elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="c216f-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="c216f-289">La validación también tiene lugar en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c216f-289">Validation also takes place on the server.</span></span> <span data-ttu-id="c216f-290">Puede que JavaScript esté deshabilitado en los clientes, mientras que hay algunas validaciones que solo se pueden realizar en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c216f-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="c216f-291">Alternativa del asistente de HTML: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="c216f-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="c216f-292">`Validation Message Tag Helper` se usa con el atributo `asp-validation-for` en un elemento HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).</span><span class="sxs-lookup"><span data-stu-id="c216f-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="c216f-293">El asistente de etiquetas de mensaje de validación generará el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="c216f-294">`Validation Message Tag Helper` suele usarse por lo general después de un asistente de etiquetas `Input` en la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="c216f-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="c216f-295">Gracias a esto, se mostrarán todos los mensajes de error de validación cerca de la entrada que produjo el error.</span><span class="sxs-lookup"><span data-stu-id="c216f-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="c216f-296">Debe tener una vista con las referencias de script de JavaScript y de [jQuery](https://jquery.com/) adecuadas para la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c216f-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="c216f-297">Consulte [validación de modelos](../models/validation.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c216f-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="c216f-298">Cuando se produce un error de validación del lado servidor (por ejemplo, porque haya una validación del lado servidor personalizada o porque la validación del lado cliente esté deshabilitada), MVC pone ese mensaje de error como cuerpo del elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="c216f-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="c216f-299">Asistente de etiquetas de resumen de validación</span><span class="sxs-lookup"><span data-stu-id="c216f-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="c216f-300">Tiene como destino todos los elementos `<div>` con el atributo `asp-validation-summary`.</span><span class="sxs-lookup"><span data-stu-id="c216f-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="c216f-301">Alternativa del asistente de HTML: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="c216f-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="c216f-302">`Validation Summary Tag Helper` se usa para mostrar un resumen de los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="c216f-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="c216f-303">El valor de atributo `asp-validation-summary` puede ser cualquiera de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="c216f-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="c216f-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="c216f-304">asp-validation-summary</span></span>|<span data-ttu-id="c216f-305">Mensajes de validación que se muestran</span><span class="sxs-lookup"><span data-stu-id="c216f-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="c216f-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="c216f-306">ValidationSummary.All</span></span>|<span data-ttu-id="c216f-307">Nivel de modelo y de propiedad</span><span class="sxs-lookup"><span data-stu-id="c216f-307">Property and model level</span></span>|
|<span data-ttu-id="c216f-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="c216f-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="c216f-309">Modelo</span><span class="sxs-lookup"><span data-stu-id="c216f-309">Model</span></span>|
|<span data-ttu-id="c216f-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="c216f-310">ValidationSummary.None</span></span>|<span data-ttu-id="c216f-311">None</span><span class="sxs-lookup"><span data-stu-id="c216f-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="c216f-312">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="c216f-312">Sample</span></span>

<span data-ttu-id="c216f-313">En el siguiente ejemplo, el modelo de datos tiene atributos `DataAnnotation`, lo que genera mensajes de error de validación sobre el elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c216f-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="c216f-314">Cuando se produce un error de validación, el asistente de etiquetas de validación muestra el mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="c216f-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="c216f-315">El código HTML generado (cuando el modelo es válido) es este:</span><span class="sxs-lookup"><span data-stu-id="c216f-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="c216f-316">Asistente de etiquetas de selección (Select)</span><span class="sxs-lookup"><span data-stu-id="c216f-316">The Select Tag Helper</span></span>

* <span data-ttu-id="c216f-317">Genera el elemento [select](https://www.w3.org/wiki/HTML/Elements/select) y el elemento asociado [option](https://www.w3.org/wiki/HTML/Elements/option) de las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="c216f-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="c216f-318">Tiene `Html.DropDownListFor` y `Html.ListBoxFor` como alternativa del asistente de HTML.</span><span class="sxs-lookup"><span data-stu-id="c216f-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="c216f-319">`Select Tag Helper` `asp-for` especifica el nombre de la propiedad de modelo del elemento [select](https://www.w3.org/wiki/HTML/Elements/select), mientras que `asp-items` especifica los elementos [option](https://www.w3.org/wiki/HTML/Elements/option).</span><span class="sxs-lookup"><span data-stu-id="c216f-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="c216f-320">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c216f-320">For example:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="c216f-321">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="c216f-322">El método `Index` inicializa `CountryViewModel`, establece el país seleccionado y lo pasa a la vista `Index`.</span><span class="sxs-lookup"><span data-stu-id="c216f-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="c216f-323">El método HTTP POST `Index` muestra la selección:</span><span class="sxs-lookup"><span data-stu-id="c216f-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="c216f-324">La vista `Index`:</span><span class="sxs-lookup"><span data-stu-id="c216f-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="c216f-325">Genera el siguiente código HTML (con la selección "CA"):</span><span class="sxs-lookup"><span data-stu-id="c216f-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="c216f-326">No se recomienda usar `ViewBag` o `ViewData` con el asistente de etiquetas Select.</span><span class="sxs-lookup"><span data-stu-id="c216f-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="c216f-327">Un modelo de vista es más eficaz a la hora de proporcionar metadatos MVC y suele ser menos problemático.</span><span class="sxs-lookup"><span data-stu-id="c216f-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="c216f-328">El valor de atributo `asp-for` es un caso especial y no necesita un prefijo `Model`, mientras que los otros atributos del asistente de etiquetas sí (como `asp-items`).</span><span class="sxs-lookup"><span data-stu-id="c216f-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="c216f-329">Enlace con enum</span><span class="sxs-lookup"><span data-stu-id="c216f-329">Enum binding</span></span>

<span data-ttu-id="c216f-330">A menudo, conviene usar `<select>` con una propiedad `enum` y generar los elementos `SelectListItem` a partir de valores `enum`.</span><span class="sxs-lookup"><span data-stu-id="c216f-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="c216f-331">Sample:</span><span class="sxs-lookup"><span data-stu-id="c216f-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="c216f-332">El método `GetEnumSelectList` genera un objeto `SelectList` para una enumeración.</span><span class="sxs-lookup"><span data-stu-id="c216f-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="c216f-333">Puede marcar la lista de enumeradores con el atributo `Display` para obtener una interfaz de usuario más completa:</span><span class="sxs-lookup"><span data-stu-id="c216f-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="c216f-334">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="c216f-335">Agrupamiento de opciones</span><span class="sxs-lookup"><span data-stu-id="c216f-335">Option Group</span></span>

<span data-ttu-id="c216f-336">El [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) elemento HTML se genera cuando el modelo de vista contiene uno o más `SelectListGroup` objetos.</span><span class="sxs-lookup"><span data-stu-id="c216f-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="c216f-337">`CountryViewModelGroup` agrupa los elementos `SelectListItem` en los grupos "North America" y "Europe":</span><span class="sxs-lookup"><span data-stu-id="c216f-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="c216f-338">Aquí mostramos los dos grupos:</span><span class="sxs-lookup"><span data-stu-id="c216f-338">The two groups are shown below:</span></span>

![Ejemplo de agrupamiento de opciones](working-with-forms/_static/grp.png)

<span data-ttu-id="c216f-340">El código HTML generado:</span><span class="sxs-lookup"><span data-stu-id="c216f-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="c216f-341">Selección múltiple</span><span class="sxs-lookup"><span data-stu-id="c216f-341">Multiple select</span></span>

<span data-ttu-id="c216f-342">El asistente de etiquetas Select generará automáticamente el atributo [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) si la propiedad especificada en el atributo `asp-for` es `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="c216f-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="c216f-343">Por ejemplo, si tenemos el siguiente modelo:</span><span class="sxs-lookup"><span data-stu-id="c216f-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="c216f-344">Con la siguiente vista:</span><span class="sxs-lookup"><span data-stu-id="c216f-344">With the following view:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="c216f-345">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="c216f-346">Sin selección</span><span class="sxs-lookup"><span data-stu-id="c216f-346">No selection</span></span>

<span data-ttu-id="c216f-347">Si ve que usa la opción "sin especificar" en varias páginas, puede crear una plantilla para no tener que repetir el código HTML:</span><span class="sxs-lookup"><span data-stu-id="c216f-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="c216f-348">La plantilla *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c216f-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="c216f-349">La adición de [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elementos HTML no se limita al caso de *ninguna selección* .</span><span class="sxs-lookup"><span data-stu-id="c216f-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="c216f-350">Por ejemplo, el método de acción y vista siguientes generarán un código HTML similar al código anterior:</span><span class="sxs-lookup"><span data-stu-id="c216f-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="c216f-351">Se seleccionará el elemento `<option>` correcto (que contenga el atributo `selected="selected"`) en función del valor real de `Country`.</span><span class="sxs-lookup"><span data-stu-id="c216f-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="c216f-352">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c216f-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="c216f-353">HTML Form element (Elemento HTML Form)</span><span class="sxs-lookup"><span data-stu-id="c216f-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="c216f-354">Request Verification Token (Token de comprobación de solicitudes)</span><span class="sxs-lookup"><span data-stu-id="c216f-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="c216f-355">IAttributeAdapter Interface (Interfaz IAttributeAdapter)</span><span class="sxs-lookup"><span data-stu-id="c216f-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="c216f-356">Fragmentos de código de este documento</span><span class="sxs-lookup"><span data-stu-id="c216f-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
