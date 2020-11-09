---
title: Validación de modelos en ASP.NET Core MVC
author: rick-anderson
description: 'Obtenga información sobre la validación de modelos en ASP.NET Core MVC y Razor páginas.'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 77d49710b9d69f6fbbe92970f1c455de32489444
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056964"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a><span data-ttu-id="7a894-103">Validación del modelo en ASP.NET Core MVC y Razor páginas</span><span class="sxs-lookup"><span data-stu-id="7a894-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a894-104">De [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7a894-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="7a894-105">En este artículo se explica cómo validar los datos proporcionados por el usuario en una aplicación ASP.NET Core MVC o Razor pages.</span><span class="sxs-lookup"><span data-stu-id="7a894-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="7a894-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7a894-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="7a894-107">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="7a894-107">Model state</span></span>

<span data-ttu-id="7a894-108">El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="7a894-109">Los errores que se originan del [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos.</span><span class="sxs-lookup"><span data-stu-id="7a894-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="7a894-110">Por ejemplo, se escribe una "x" en un campo numérico entero.</span><span class="sxs-lookup"><span data-stu-id="7a894-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="7a894-111">La validación del modelo se produce después del enlace de modelos y notifica los errores en los que los datos no cumplen las reglas de negocio.</span><span class="sxs-lookup"><span data-stu-id="7a894-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="7a894-112">Por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5.</span><span class="sxs-lookup"><span data-stu-id="7a894-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="7a894-113">Tanto el enlace de modelos como la validación de modelos se producen antes de la ejecución de una acción de controlador o un Razor método de controlador de páginas.</span><span class="sxs-lookup"><span data-stu-id="7a894-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="7a894-114">En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada.</span><span class="sxs-lookup"><span data-stu-id="7a894-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="7a894-115">Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="7a894-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="7a894-116">Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="7a894-117">En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="7a894-118">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="7a894-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="7a894-119">Nueva ejecución de la validación</span><span class="sxs-lookup"><span data-stu-id="7a894-119">Rerun validation</span></span>

<span data-ttu-id="7a894-120">La validación es automática, pero tal vez le interese repetirla manualmente.</span><span class="sxs-lookup"><span data-stu-id="7a894-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="7a894-121">Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado.</span><span class="sxs-lookup"><span data-stu-id="7a894-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="7a894-122">Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="7a894-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="7a894-123">Atributos de validación</span><span class="sxs-lookup"><span data-stu-id="7a894-123">Validation attributes</span></span>

<span data-ttu-id="7a894-124">Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7a894-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="7a894-125">En el ejemplo siguiente de la aplicación de ejemplo se muestra una clase de modelo anotada con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="7a894-126">El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados.</span><span class="sxs-lookup"><span data-stu-id="7a894-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="7a894-127">`[ClassicMovieWithClientValidator]` no se muestra.</span><span class="sxs-lookup"><span data-stu-id="7a894-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="7a894-128">`[ClassicMovieWithClientValidator]` muestra una manera alternativa de implementar un atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="7a894-129">Atributos integrados</span><span class="sxs-lookup"><span data-stu-id="7a894-129">Built-in attributes</span></span>

<span data-ttu-id="7a894-130">Estos son algunos de los atributos de validación integrados:</span><span class="sxs-lookup"><span data-stu-id="7a894-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="7a894-131">`[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito.</span><span class="sxs-lookup"><span data-stu-id="7a894-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="7a894-132">Requiere [métodos adicionales de validación de jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="7a894-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="7a894-133">`[Compare]`: Valida que dos propiedades de un modelo coincidan.</span><span class="sxs-lookup"><span data-stu-id="7a894-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="7a894-134">`[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="7a894-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="7a894-135">`[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.</span><span class="sxs-lookup"><span data-stu-id="7a894-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="7a894-136">`[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="7a894-137">`[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.</span><span class="sxs-lookup"><span data-stu-id="7a894-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="7a894-138">`[Required]`: Valida que el campo no sea NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="7a894-139">Vea el [ `[Required]` atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="7a894-140">`[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="7a894-141">`[Url]`: Valida que la propiedad tiene un formato de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7a894-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="7a894-142">`[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="7a894-143">Vea el [ `[Remote]` atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="7a894-144">En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="7a894-145">Mensajes de error</span><span class="sxs-lookup"><span data-stu-id="7a894-145">Error messages</span></span>

<span data-ttu-id="7a894-146">Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="7a894-147">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="7a894-148">Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales.</span><span class="sxs-lookup"><span data-stu-id="7a894-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="7a894-149">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="7a894-150">Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".</span><span class="sxs-lookup"><span data-stu-id="7a894-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="7a894-151">Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="7a894-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="7a894-152">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="7a894-152">[Required] attribute</span></span>

<span data-ttu-id="7a894-153">El sistema de validación de .NET Core 3.0 y versiones posteriores trata las propiedades enlazadas o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-153">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="7a894-154">Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-154">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="7a894-155">Este comportamiento se puede deshabilitar si se configura <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a894-155">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="7a894-156">Validación de [Required] en el servidor</span><span class="sxs-lookup"><span data-stu-id="7a894-156">[Required] validation on the server</span></span>

<span data-ttu-id="7a894-157">En el servidor, si la propiedad es NULL, se considera que falta un valor requerido.</span><span class="sxs-lookup"><span data-stu-id="7a894-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="7a894-158">Un campo que no acepta valores NULL siempre es válido y el mensaje de error del atributo `[Required]` no se muestra nunca.</span><span class="sxs-lookup"><span data-stu-id="7a894-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="7a894-159">Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="7a894-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="7a894-160">Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="7a894-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="7a894-161">Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="7a894-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="7a894-162">[Acepta \<T> valores NULL](/dotnet/csharp/programming-guide/nullable-types/) los tipos de valor se tratan como los tipos estándar que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="7a894-163">Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="7a894-164">Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="7a894-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="7a894-165">Validación de [Required] en el cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-165">[Required] validation on the client</span></span>

<span data-ttu-id="7a894-166">Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="7a894-167">En el cliente:</span><span class="sxs-lookup"><span data-stu-id="7a894-167">On the client:</span></span>

* <span data-ttu-id="7a894-168">Un valor se considera presente solo si se especifica una entrada para él.</span><span class="sxs-lookup"><span data-stu-id="7a894-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="7a894-169">Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="7a894-170">El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="7a894-171">La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.</span><span class="sxs-lookup"><span data-stu-id="7a894-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="7a894-172">Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="7a894-173">Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="7a894-174">Si no usa el atributo, aparecerá un mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a894-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="7a894-175">Para especificar un mensaje de error personalizado, use el atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="7a894-176">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="7a894-176">[Remote] attribute</span></span>

<span data-ttu-id="7a894-177">El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="7a894-178">Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.</span><span class="sxs-lookup"><span data-stu-id="7a894-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="7a894-179">Para implementar la validación remota:</span><span class="sxs-lookup"><span data-stu-id="7a894-179">To implement remote validation:</span></span>

1. <span data-ttu-id="7a894-180">Cree un método de acción para que lo llame JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7a894-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="7a894-181">El método [remoto](https://jqueryvalidation.org/remote-method/) de validación de jQuery espera una respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7a894-181">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="7a894-182">`true` significa que los datos de entrada son válidos.</span><span class="sxs-lookup"><span data-stu-id="7a894-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="7a894-183">`false`, `undefined` o `null` significan que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="7a894-184">Muestre el mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a894-184">Display the default error message.</span></span>
   * <span data-ttu-id="7a894-185">Cualquier otra cadena significa que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="7a894-186">Muestre la cadena como un mensaje de error personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="7a894-187">A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:</span><span class="sxs-lookup"><span data-stu-id="7a894-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="7a894-188">En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="7a894-189">El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="7a894-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="7a894-190">Campos adicionales</span><span class="sxs-lookup"><span data-stu-id="7a894-190">Additional fields</span></span>

<span data-ttu-id="7a894-191">La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="7a894-192">Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres.</span><span class="sxs-lookup"><span data-stu-id="7a894-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="7a894-193">En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="7a894-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="7a894-194">`AdditionalFields` podría configurarse explícitamente para las cadenas "FirstName" y "LastName", pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof), se simplifica la refactorización posterior.</span><span class="sxs-lookup"><span data-stu-id="7a894-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="7a894-195">El método de acción para esta validación debe aceptar los argumentos `firstName` y `lastName`:</span><span class="sxs-lookup"><span data-stu-id="7a894-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="7a894-196">Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.</span><span class="sxs-lookup"><span data-stu-id="7a894-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="7a894-197">Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="7a894-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="7a894-198">Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="7a894-199">`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante.</span><span class="sxs-lookup"><span data-stu-id="7a894-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="7a894-200">Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="7a894-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="7a894-201">Alternativas a los atributos integrados</span><span class="sxs-lookup"><span data-stu-id="7a894-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="7a894-202">Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="7a894-203">[Crear atributos personalizados](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="7a894-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="7a894-204">[Implementar IValidatableObject](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="7a894-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="7a894-205">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="7a894-205">Custom attributes</span></span>

<span data-ttu-id="7a894-206">Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados.</span><span class="sxs-lookup"><span data-stu-id="7a894-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="7a894-207">Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="7a894-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="7a894-208">El método `IsValid` acepta un objeto denominado *value* , que es la entrada que se va a validar.</span><span class="sxs-lookup"><span data-stu-id="7a894-208">The `IsValid` method accepts an object named *value* , which is the input to be validated.</span></span> <span data-ttu-id="7a894-209">Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="7a894-210">El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="7a894-211">El atributo `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="7a894-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="7a894-212">Solo se ejecuta en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-212">Is only run on the server.</span></span>
* <span data-ttu-id="7a894-213">En el caso de las películas clásicas, valida la fecha de lanzamiento:</span><span class="sxs-lookup"><span data-stu-id="7a894-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="7a894-214">La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario.</span><span class="sxs-lookup"><span data-stu-id="7a894-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="7a894-215">Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="7a894-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="7a894-216">IValidatableObject</span></span>

<span data-ttu-id="7a894-217">El ejemplo anterior solo funciona con tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7a894-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="7a894-218">Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="7a894-219">Validación de nodo de nivel superior</span><span class="sxs-lookup"><span data-stu-id="7a894-219">Top-level node validation</span></span>

<span data-ttu-id="7a894-220">Los nodos de nivel superior incluyen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-220">Top-level nodes include:</span></span>

* <span data-ttu-id="7a894-221">Parámetros de acción</span><span class="sxs-lookup"><span data-stu-id="7a894-221">Action parameters</span></span>
* <span data-ttu-id="7a894-222">Propiedades de controlador</span><span class="sxs-lookup"><span data-stu-id="7a894-222">Controller properties</span></span>
* <span data-ttu-id="7a894-223">Parámetros de controlador de página</span><span class="sxs-lookup"><span data-stu-id="7a894-223">Page handler parameters</span></span>
* <span data-ttu-id="7a894-224">Propiedades de modelo de página</span><span class="sxs-lookup"><span data-stu-id="7a894-224">Page model properties</span></span>

<span data-ttu-id="7a894-225">Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7a894-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="7a894-226">En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:</span><span class="sxs-lookup"><span data-stu-id="7a894-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="7a894-227">Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="7a894-228">En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:</span><span class="sxs-lookup"><span data-stu-id="7a894-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="7a894-229">En la página Comprobar edad ( *CheckAge.cshtml* ), hay dos formularios.</span><span class="sxs-lookup"><span data-stu-id="7a894-229">In the Check Age page ( *CheckAge.cshtml* ), there are two forms.</span></span> <span data-ttu-id="7a894-230">El primer formulario envía un valor `Age` de `99` como una cadena de parámetro de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="7a894-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="7a894-231">Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.</span><span class="sxs-lookup"><span data-stu-id="7a894-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="7a894-232">El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="7a894-233">Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="7a894-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="7a894-234">Número máximo de errores</span><span class="sxs-lookup"><span data-stu-id="7a894-234">Maximum errors</span></span>

<span data-ttu-id="7a894-235">La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="7a894-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="7a894-236">Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a894-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="7a894-237">Recursividad máxima</span><span class="sxs-lookup"><span data-stu-id="7a894-237">Maximum recursion</span></span>

<span data-ttu-id="7a894-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando.</span><span class="sxs-lookup"><span data-stu-id="7a894-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="7a894-239">En el caso de los modelos profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila.</span><span class="sxs-lookup"><span data-stu-id="7a894-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="7a894-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada.</span><span class="sxs-lookup"><span data-stu-id="7a894-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="7a894-241">El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32.</span><span class="sxs-lookup"><span data-stu-id="7a894-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="7a894-242">Cortocircuito automático</span><span class="sxs-lookup"><span data-stu-id="7a894-242">Automatic short-circuit</span></span>

<span data-ttu-id="7a894-243">La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="7a894-244">Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.</span><span class="sxs-lookup"><span data-stu-id="7a894-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="7a894-245">Deshabilitación de la validación</span><span class="sxs-lookup"><span data-stu-id="7a894-245">Disable validation</span></span>

<span data-ttu-id="7a894-246">Para deshabilitar la validación:</span><span class="sxs-lookup"><span data-stu-id="7a894-246">To disable validation:</span></span>

1. <span data-ttu-id="7a894-247">Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="7a894-248">Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="7a894-249">Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="7a894-250">Validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-250">Client-side validation</span></span>

<span data-ttu-id="7a894-251">La validación del lado cliente impide realizar el envío hasta que el formulario sea válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="7a894-252">El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="7a894-253">La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario.</span><span class="sxs-lookup"><span data-stu-id="7a894-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="7a894-254">Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="7a894-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="7a894-255">El script de [validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca de front-end de Microsoft personalizada que se basa en el conocido complemento de [validación de jQuery](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="7a894-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="7a894-256">Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="7a894-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="7a894-257">En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="7a894-258">la validación discreta de jQuery analiza los `data-` atributos y pasa la lógica a la validación de jQuery y "copia" la lógica de validación del lado servidor al cliente.</span><span class="sxs-lookup"><span data-stu-id="7a894-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="7a894-259">Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="7a894-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="7a894-260">Los anteriores asistentes de etiquetas representan el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="7a894-260">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="7a894-261">Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="7a894-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="7a894-262">El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno.</span><span class="sxs-lookup"><span data-stu-id="7a894-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="7a894-263">la validación discreta de jQuery pasa este valor al método de validación de jQuery [Required ()](https://jqueryvalidation.org/required-method/) , que después muestra ese mensaje en el **\<span>** elemento correspondiente.</span><span class="sxs-lookup"><span data-stu-id="7a894-263">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="7a894-264">La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="7a894-265">Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes.</span><span class="sxs-lookup"><span data-stu-id="7a894-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="7a894-266">Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="7a894-267">Validación discreta</span><span class="sxs-lookup"><span data-stu-id="7a894-267">Unobtrusive validation</span></span>

<span data-ttu-id="7a894-268">Para obtener información sobre la validación discreta, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="7a894-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="7a894-269">Agregar validación a formularios dinámicos</span><span class="sxs-lookup"><span data-stu-id="7a894-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="7a894-270">la validación discreta de jQuery pasa la lógica de validación y los parámetros a la validación de jQuery la primera vez que se carga la página.</span><span class="sxs-lookup"><span data-stu-id="7a894-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="7a894-271">Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="7a894-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="7a894-272">Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación.</span><span class="sxs-lookup"><span data-stu-id="7a894-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="7a894-273">Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.</span><span class="sxs-lookup"><span data-stu-id="7a894-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="7a894-274">El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento.</span><span class="sxs-lookup"><span data-stu-id="7a894-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="7a894-275">Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector.</span><span class="sxs-lookup"><span data-stu-id="7a894-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="7a894-276">Los valores de esos atributos se pasan al complemento de validación de jQuery.</span><span class="sxs-lookup"><span data-stu-id="7a894-276">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="7a894-277">Agregar validación a controles dinámicos</span><span class="sxs-lookup"><span data-stu-id="7a894-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="7a894-278">El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="7a894-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="7a894-279">Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="7a894-280">Validación del lado cliente personalizada</span><span class="sxs-lookup"><span data-stu-id="7a894-280">Custom client-side validation</span></span>

<span data-ttu-id="7a894-281">La validación del lado cliente personalizada se realiza mediante `data-` la generación de atributos HTML que funcionan con un adaptador de validación de jQuery personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="7a894-282">El siguiente ejemplo de código de adaptador se escribió para los atributos `[ClassicMovie]` y `[ClassicMovieWithClientValidator]` que se introdujeron anteriormente en este artículo:</span><span class="sxs-lookup"><span data-stu-id="7a894-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="7a894-283">Para obtener información sobre cómo escribir adaptadores, vea la [documentación de validación de jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="7a894-283">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="7a894-284">El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="7a894-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="7a894-285">Marcan que el campo está sujeto a validación (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="7a894-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="7a894-286">Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="7a894-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="7a894-287">Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="7a894-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="7a894-288">En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="7a894-289">Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="7a894-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="7a894-290">Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="7a894-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="7a894-291">Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="7a894-292">Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes.</span><span class="sxs-lookup"><span data-stu-id="7a894-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="7a894-293">El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.</span><span class="sxs-lookup"><span data-stu-id="7a894-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="7a894-294">Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7a894-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="7a894-295">Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="7a894-296">AttributeAdapter para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="7a894-297">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7a894-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="7a894-298">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="7a894-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="7a894-299">Cree una clase de adaptador de atributo para el atributo de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="7a894-300">Derive la clase de [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="7a894-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="7a894-301">Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="7a894-302">Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="7a894-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="7a894-303">En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="7a894-304">Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a894-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="7a894-305">IClientModelValidator para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="7a894-306">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovieWithClientValidator` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7a894-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="7a894-307">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="7a894-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="7a894-308">En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="7a894-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="7a894-309">En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="7a894-310">Deshabilitación de la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-310">Disable client-side validation</span></span>

<span data-ttu-id="7a894-311">El código siguiente deshabilita la validación de cliente en Razor las páginas:</span><span class="sxs-lookup"><span data-stu-id="7a894-311">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="7a894-312">Otras opciones para deshabilitar la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="7a894-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="7a894-313">Convierta en comentario la referencia a `_ValidationScriptsPartial` en todos los archivos *.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7a894-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="7a894-314">Quite el contenido del archivo *Pages\Shared\_ValidationScriptsPartial.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7a894-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="7a894-315">El enfoque anterior no impedirá la validación del lado cliente de la ASP.NET Core Identity Razor biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="7a894-315">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="7a894-316">Para obtener más información, vea <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="7a894-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a894-317">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7a894-317">Additional resources</span></span>

* [<span data-ttu-id="7a894-318">Espacio de nombres System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="7a894-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="7a894-319">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="7a894-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a894-320">En este artículo se explica cómo validar los datos proporcionados por el usuario en una aplicación ASP.NET Core MVC o Razor pages.</span><span class="sxs-lookup"><span data-stu-id="7a894-320">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="7a894-321">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7a894-321">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="7a894-322">Estado del modelo</span><span class="sxs-lookup"><span data-stu-id="7a894-322">Model state</span></span>

<span data-ttu-id="7a894-323">El estado del modelo representa los errores que proceden de dos subsistemas: el enlace de modelos y la validación de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="7a894-324">Los errores que se originan en el [enlace de modelos](model-binding.md) suelen ser errores de conversión de datos (por ejemplo, se especifica una "x" en un campo que espera un entero).</span><span class="sxs-lookup"><span data-stu-id="7a894-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="7a894-325">La validación de modelos se produce después de enlace de modelos y notifica errores cuando los datos no se ajustan a las reglas de negocios (por ejemplo, se especifica un 0 en un campo que espera una clasificación entre 1 y 5).</span><span class="sxs-lookup"><span data-stu-id="7a894-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="7a894-326">Tanto el enlace de modelos como la validación se producen antes de la ejecución de una acción de controlador o un Razor método de controlador de páginas.</span><span class="sxs-lookup"><span data-stu-id="7a894-326">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="7a894-327">En el caso de las aplicaciones web, la aplicación es responsable de inspeccionar `ModelState.IsValid` y reaccionar de manera apropiada.</span><span class="sxs-lookup"><span data-stu-id="7a894-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="7a894-328">Normalmente, las aplicaciones web vuelven a mostrar la página con un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="7a894-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="7a894-329">Los controladores de Web API no tienen que comprobar `ModelState.IsValid` si tienen el atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="7a894-330">En ese caso, se devuelve una respuesta HTTP 400 automática que contiene los detalles del error cuando el estado del modelo no es válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="7a894-331">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="7a894-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="7a894-332">Nueva ejecución de la validación</span><span class="sxs-lookup"><span data-stu-id="7a894-332">Rerun validation</span></span>

<span data-ttu-id="7a894-333">La validación es automática, pero tal vez le interese repetirla manualmente.</span><span class="sxs-lookup"><span data-stu-id="7a894-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="7a894-334">Por ejemplo, tal vez haya calculado el valor de una propiedad y quiera volver a ejecutar la validación después de establecer la propiedad en el valor calculado.</span><span class="sxs-lookup"><span data-stu-id="7a894-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="7a894-335">Para volver a ejecutar la validación, llame al método `TryValidateModel`, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="7a894-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="7a894-336">Atributos de validación</span><span class="sxs-lookup"><span data-stu-id="7a894-336">Validation attributes</span></span>

<span data-ttu-id="7a894-337">Los atributos de validación permiten especificar reglas de validación para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7a894-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="7a894-338">En el ejemplo siguiente de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) se muestra una clase de modelo anotada con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-338">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="7a894-339">El atributo `[ClassicMovie]` es un atributo de validación personalizado y los demás están integrados.</span><span class="sxs-lookup"><span data-stu-id="7a894-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="7a894-340">No se muestra `[ClassicMovie2]`, que indica una manera alternativa de implementar un atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="7a894-341">Atributos integrados</span><span class="sxs-lookup"><span data-stu-id="7a894-341">Built-in attributes</span></span>

<span data-ttu-id="7a894-342">Entre los atributos de validación integrados se incluyen:</span><span class="sxs-lookup"><span data-stu-id="7a894-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="7a894-343">`[CreditCard]`: Valida que la propiedad tiene un formato de tarjeta de crédito.</span><span class="sxs-lookup"><span data-stu-id="7a894-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="7a894-344">`[Compare]`: Valida que dos propiedades de un modelo coincidan.</span><span class="sxs-lookup"><span data-stu-id="7a894-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="7a894-345">Por ejemplo, el archivo *Register.cshtml.cs* usa `[Compare]` para validar que ambas contraseñas escritas coincidan.</span><span class="sxs-lookup"><span data-stu-id="7a894-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="7a894-346">[Scaffolding Identity ](xref:security/authentication/scaffold-identity) para ver el código de registro.</span><span class="sxs-lookup"><span data-stu-id="7a894-346">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="7a894-347">`[EmailAddress]`: Valida que la propiedad tiene un formato de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="7a894-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="7a894-348">`[Phone]`: Valida que la propiedad tiene un formato de número de teléfono.</span><span class="sxs-lookup"><span data-stu-id="7a894-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="7a894-349">`[Range]`: Valida que el valor de la propiedad se encuentra dentro de un intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="7a894-350">`[RegularExpression]`: Valida que el valor de propiedad coincide con una expresión regular especificada.</span><span class="sxs-lookup"><span data-stu-id="7a894-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="7a894-351">`[Required]`: Valida que el campo no sea NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="7a894-352">Vea el [ `[Required]` atributo](#required-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="7a894-353">`[StringLength]`: Valida que un valor de propiedad de cadena no supera un límite de longitud especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="7a894-354">`[Url]`: Valida que la propiedad tiene un formato de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7a894-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="7a894-355">`[Remote]`: Valida la entrada en el cliente mediante una llamada a un método de acción en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="7a894-356">Vea el [ `[Remote]` atributo](#remote-attribute) para obtener más información sobre el comportamiento de este atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="7a894-357">Cuando se usa el atributo `[RegularExpression]` con la validación del lado cliente, la regex se ejecuta en JavaScript en el cliente.</span><span class="sxs-lookup"><span data-stu-id="7a894-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="7a894-358">Esto significa que se usará el comportamiento de coincidencia de [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior).</span><span class="sxs-lookup"><span data-stu-id="7a894-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="7a894-359">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="7a894-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="7a894-360">En el espacio de nombres [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) encontrará una lista completa de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="7a894-361">Mensajes de error</span><span class="sxs-lookup"><span data-stu-id="7a894-361">Error messages</span></span>

<span data-ttu-id="7a894-362">Los atributos de validación permiten especificar el mensaje de error que se mostrará para una entrada no válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="7a894-363">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="7a894-364">Internamente, los atributos llaman a `String.Format` con un marcador de posición para el nombre de campo y, en ocasiones, marcadores de posición adicionales.</span><span class="sxs-lookup"><span data-stu-id="7a894-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="7a894-365">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="7a894-366">Cuando se aplica a una propiedad `Name`, el mensaje de error creado por el código anterior sería "La longitud del nombre debe estar entre 6 y 8".</span><span class="sxs-lookup"><span data-stu-id="7a894-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="7a894-367">Para averiguar qué parámetros se pasan a `String.Format` para el mensaje de error de un atributo determinado, vea el [código fuente de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="7a894-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="7a894-368">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="7a894-368">[Required] attribute</span></span>

<span data-ttu-id="7a894-369">De forma predeterminada, el sistema de validación trata las propiedades o los parámetros que no aceptan valores NULL como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="7a894-370">Los [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` y `int` no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="7a894-371">Validación de [Required] en el servidor</span><span class="sxs-lookup"><span data-stu-id="7a894-371">[Required] validation on the server</span></span>

<span data-ttu-id="7a894-372">En el servidor, si la propiedad es NULL, se considera que falta un valor requerido.</span><span class="sxs-lookup"><span data-stu-id="7a894-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="7a894-373">Un campo que no acepta valores NULL siempre es válido, y el mensaje de error del atributo [Required] no se muestra nunca.</span><span class="sxs-lookup"><span data-stu-id="7a894-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="7a894-374">Aun así, el enlace de modelos para una propiedad que no acepta valores NULL podría fallar, lo que genera un mensaje de error como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="7a894-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="7a894-375">Para especificar un mensaje de error personalizado para la validación del lado servidor de tipos que no aceptan valores NULL, tiene las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="7a894-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="7a894-376">Haga que el campo acepte valores NULL (por ejemplo, `decimal?` en lugar de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="7a894-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="7a894-377">[Acepta \<T> valores NULL](/dotnet/csharp/programming-guide/nullable-types/) los tipos de valor se tratan como los tipos estándar que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="7a894-378">Especifique el mensaje de error predeterminado que el enlace de modelos va a usar, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="7a894-379">Para obtener más información sobre los errores de enlace de modelos para los que se pueden establecer mensajes predeterminados, vea <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="7a894-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="7a894-380">Validación de [Required] en el cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-380">[Required] validation on the client</span></span>

<span data-ttu-id="7a894-381">Las cadenas y los tipos que no aceptan valores NULL se tratan de forma diferente en el cliente, en comparación con el servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="7a894-382">En el cliente:</span><span class="sxs-lookup"><span data-stu-id="7a894-382">On the client:</span></span>

* <span data-ttu-id="7a894-383">Un valor se considera presente solo si se especifica una entrada para él.</span><span class="sxs-lookup"><span data-stu-id="7a894-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="7a894-384">Por lo tanto, la validación del lado cliente controla los tipos que no aceptan valores NULL del mismo modo que los tipos que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="7a894-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="7a894-385">El método [required](https://jqueryvalidation.org/required-method/) de jQuery Validate considera que un espacio en blanco en un campo de cadena es una entrada válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="7a894-386">La validación del lado servidor considera que un campo de cadena necesario no es válido si solo se especifica un espacio en blanco.</span><span class="sxs-lookup"><span data-stu-id="7a894-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="7a894-387">Como se indicó anteriormente, los tipos que no aceptan valores NULL se tratan como si tuvieran un atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="7a894-388">Esto significa que se obtiene la validación del lado cliente incluso si no se aplica el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="7a894-389">Si no usa el atributo, aparecerá un mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a894-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="7a894-390">Para especificar un mensaje de error personalizado, use el atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="7a894-391">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="7a894-391">[Remote] attribute</span></span>

<span data-ttu-id="7a894-392">El atributo `[Remote]` implementa la validación del lado cliente que requiere llamar a un método en el servidor para determinar si la entrada del campo es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="7a894-393">Por ejemplo, la aplicación podría tener que comprobar si un nombre de usuario ya está en uso.</span><span class="sxs-lookup"><span data-stu-id="7a894-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="7a894-394">Para implementar la validación remota:</span><span class="sxs-lookup"><span data-stu-id="7a894-394">To implement remote validation:</span></span>

1. <span data-ttu-id="7a894-395">Cree un método de acción para que lo llame JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7a894-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="7a894-396">El método [remote](https://jqueryvalidation.org/remote-method/) de jQuery Validate espera una respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7a894-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="7a894-397">`"true"` significa que los datos de entrada son válidos.</span><span class="sxs-lookup"><span data-stu-id="7a894-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="7a894-398">`"false"`, `undefined` o `null` significan que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="7a894-399">Muestre el mensaje de error predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7a894-399">Display the default error message.</span></span>
   * <span data-ttu-id="7a894-400">Cualquier otra cadena significa que la entrada no es válida.</span><span class="sxs-lookup"><span data-stu-id="7a894-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="7a894-401">Muestre la cadena como un mensaje de error personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="7a894-402">A continuación encontrará un ejemplo de un método de acción que devuelve un mensaje de error personalizado:</span><span class="sxs-lookup"><span data-stu-id="7a894-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="7a894-403">En la clase de modelo, anote la propiedad con un atributo `[Remote]` que apunte al método de acción de validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="7a894-404">El atributo `[Remote]` está en el espacio de nombres `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="7a894-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="7a894-405">Instale el paquete de NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) si no usa `Microsoft.AspNetCore.App` o el metapaquete `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="7a894-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="7a894-406">Campos adicionales</span><span class="sxs-lookup"><span data-stu-id="7a894-406">Additional fields</span></span>

<span data-ttu-id="7a894-407">La propiedad `AdditionalFields` del atributo `[Remote]` permite validar combinaciones de campos con los datos del servidor.</span><span class="sxs-lookup"><span data-stu-id="7a894-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="7a894-408">Por ejemplo, si el modelo `User` tuviera las propiedades `FirstName` y `LastName`, podría interesarle comprobar que ningún usuario actual tuviera ya ese par de nombres.</span><span class="sxs-lookup"><span data-stu-id="7a894-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="7a894-409">En el siguiente ejemplo se muestra cómo usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="7a894-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="7a894-410">`AdditionalFields` podría configurarse explícitamente para las cadenas `"FirstName"` y `"LastName"`, pero, al usar el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) se simplifica la refactorización posterior.</span><span class="sxs-lookup"><span data-stu-id="7a894-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="7a894-411">El método de acción para esta validación debe aceptar los argumentos de nombre y apellido:</span><span class="sxs-lookup"><span data-stu-id="7a894-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="7a894-412">Cuando el usuario escribe un nombre o un apellido, JavaScript realiza una llamada remota para comprobar si ese par de nombres ya existe.</span><span class="sxs-lookup"><span data-stu-id="7a894-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="7a894-413">Para validar dos o más campos adicionales, proporciónelos como una lista delimitada por comas.</span><span class="sxs-lookup"><span data-stu-id="7a894-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="7a894-414">Por ejemplo, para agregar una propiedad `MiddleName` al modelo, establezca el atributo `[Remote]` tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="7a894-415">`AdditionalFields`, al igual que todos los argumentos de atributo, debe ser una expresión constante.</span><span class="sxs-lookup"><span data-stu-id="7a894-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="7a894-416">Por lo tanto, no use una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ni llame a <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="7a894-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="7a894-417">Alternativas a los atributos integrados</span><span class="sxs-lookup"><span data-stu-id="7a894-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="7a894-418">Si necesita una validación que no proporcionan los atributos integrados, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="7a894-419">[Crear atributos personalizados](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="7a894-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="7a894-420">[Implementar IValidatableObject](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="7a894-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="7a894-421">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="7a894-421">Custom attributes</span></span>

<span data-ttu-id="7a894-422">Para los escenarios que no se controlan mediante los atributos de validación integrados, puede crear atributos de validación personalizados.</span><span class="sxs-lookup"><span data-stu-id="7a894-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="7a894-423">Cree una clase que herede de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> y reemplace el método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="7a894-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="7a894-424">El método `IsValid` acepta un objeto denominado *value* , que es la entrada que se va a validar.</span><span class="sxs-lookup"><span data-stu-id="7a894-424">The `IsValid` method accepts an object named *value* , which is the input to be validated.</span></span> <span data-ttu-id="7a894-425">Una sobrecarga también acepta un objeto `ValidationContext`, que proporciona información adicional, como la instancia del modelo creada por el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="7a894-426">El siguiente ejemplo valida que la fecha de lanzamiento de una película del género *Classic* no sea posterior a un año especificado.</span><span class="sxs-lookup"><span data-stu-id="7a894-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="7a894-427">El atributo `[ClassicMovie2]` comprueba primero el género y continúa únicamente si es *Classic* .</span><span class="sxs-lookup"><span data-stu-id="7a894-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic* .</span></span> <span data-ttu-id="7a894-428">Para las películas que se identifican como clásicos, comprueba la fecha de lanzamiento a fin de asegurarse de que no sea posterior al límite que se ha pasado al constructor de atributo.</span><span class="sxs-lookup"><span data-stu-id="7a894-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="7a894-429">La variable `movie` del ejemplo anterior representa un objeto `Movie` que contiene los datos del envío del formulario.</span><span class="sxs-lookup"><span data-stu-id="7a894-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="7a894-430">El método `IsValid` comprueba la fecha y el género.</span><span class="sxs-lookup"><span data-stu-id="7a894-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="7a894-431">Si la validación es correcta, `IsValid` devuelve un código `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="7a894-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="7a894-432">Si se produce un error de validación, se devuelve un `ValidationResult` con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="7a894-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="7a894-433">IValidatableObject</span></span>

<span data-ttu-id="7a894-434">El ejemplo anterior solo funciona con tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7a894-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="7a894-435">Otra opción para la validación del nivel de clase consiste en implementar `IValidatableObject` en la clase de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="7a894-436">Validación de nodo de nivel superior</span><span class="sxs-lookup"><span data-stu-id="7a894-436">Top-level node validation</span></span>

<span data-ttu-id="7a894-437">Los nodos de nivel superior incluyen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-437">Top-level nodes include:</span></span>

* <span data-ttu-id="7a894-438">Parámetros de acción</span><span class="sxs-lookup"><span data-stu-id="7a894-438">Action parameters</span></span>
* <span data-ttu-id="7a894-439">Propiedades de controlador</span><span class="sxs-lookup"><span data-stu-id="7a894-439">Controller properties</span></span>
* <span data-ttu-id="7a894-440">Parámetros de controlador de página</span><span class="sxs-lookup"><span data-stu-id="7a894-440">Page handler parameters</span></span>
* <span data-ttu-id="7a894-441">Propiedades de modelo de página</span><span class="sxs-lookup"><span data-stu-id="7a894-441">Page model properties</span></span>

<span data-ttu-id="7a894-442">Los nodos de nivel superior enlazados al modelo se validan además de la validación de las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="7a894-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="7a894-443">En el ejemplo siguiente de la aplicación de muestra, el método `VerifyPhone` usa <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar el parámetro de acción `phone`:</span><span class="sxs-lookup"><span data-stu-id="7a894-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="7a894-444">Los nodos de nivel superior pueden usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> con atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="7a894-445">En el ejemplo siguiente de la aplicación de muestra, el método `CheckAge` especifica que el parámetro `age` debe estar enlazado desde la cadena de consulta al enviar el formulario:</span><span class="sxs-lookup"><span data-stu-id="7a894-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="7a894-446">En la página Comprobar edad ( *CheckAge.cshtml* ), hay dos formularios.</span><span class="sxs-lookup"><span data-stu-id="7a894-446">In the Check Age page ( *CheckAge.cshtml* ), there are two forms.</span></span> <span data-ttu-id="7a894-447">El primer formulario envía un valor `Age` de `99` como una cadena de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="7a894-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="7a894-448">Al enviar un parámetro `age` con un formato correcto desde la cadena de consulta, el formulario se valida.</span><span class="sxs-lookup"><span data-stu-id="7a894-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="7a894-449">El segundo formulario de la página Comprobar edad envía el valor `Age` en el cuerpo de la solicitud, y se produce un error de validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="7a894-450">Se produce un error en el enlace porque el parámetro `age` debe provenir de una cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="7a894-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="7a894-451">Cuando se ejecuta con `CompatibilityVersion.Version_2_1` o versiones posteriores, la validación de nodo de nivel superior está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7a894-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="7a894-452">En caso contrario, la validación del nodo de nivel superior está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="7a894-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="7a894-453">La opción predeterminada se puede invalidar si se establece la propiedad <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> en (`Startup.ConfigureServices`), como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="7a894-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="7a894-454">Número máximo de errores</span><span class="sxs-lookup"><span data-stu-id="7a894-454">Maximum errors</span></span>

<span data-ttu-id="7a894-455">La validación se detiene cuando se alcanza el número máximo de errores (200 de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="7a894-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="7a894-456">Puede configurar este número con el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a894-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="7a894-457">Recursividad máxima</span><span class="sxs-lookup"><span data-stu-id="7a894-457">Maximum recursion</span></span>

<span data-ttu-id="7a894-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> recorre el gráfico de objetos del modelo que se está validando.</span><span class="sxs-lookup"><span data-stu-id="7a894-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="7a894-459">En el caso de los modelos muy profundos o infinitamente recursivos, la validación podría causar un desbordamiento de pila.</span><span class="sxs-lookup"><span data-stu-id="7a894-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="7a894-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) proporciona una manera de detener pronto la validación si la recursividad del visitante supera la profundidad configurada.</span><span class="sxs-lookup"><span data-stu-id="7a894-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="7a894-461">El valor predeterminado de `MvcOptions.MaxValidationDepth` es 32 cuando se ejecuta con `CompatibilityVersion.Version_2_2` o una versión posterior.</span><span class="sxs-lookup"><span data-stu-id="7a894-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="7a894-462">Para las versiones anteriores, el valor es NULL, lo que significa que no hay restricción de profundidad.</span><span class="sxs-lookup"><span data-stu-id="7a894-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="7a894-463">Cortocircuito automático</span><span class="sxs-lookup"><span data-stu-id="7a894-463">Automatic short-circuit</span></span>

<span data-ttu-id="7a894-464">La validación cortocircuita (se omite) automáticamente si el gráfico de modelo no requiere validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="7a894-465">Entre los objetos para los que el tiempo de ejecución omite la validación se incluyen las colecciones de elementos primitivos (como `byte[]`, `string[]` y `Dictionary<string, string>`) y gráficos de objeto complejo que no tienen los validadores.</span><span class="sxs-lookup"><span data-stu-id="7a894-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="7a894-466">Deshabilitación de la validación</span><span class="sxs-lookup"><span data-stu-id="7a894-466">Disable validation</span></span>

<span data-ttu-id="7a894-467">Para deshabilitar la validación:</span><span class="sxs-lookup"><span data-stu-id="7a894-467">To disable validation:</span></span>

1. <span data-ttu-id="7a894-468">Cree una implementación de `IObjectModelValidator` que no marque ningún campo como no válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="7a894-469">Agregue el código siguiente a `Startup.ConfigureServices` para reemplazar la implementación predeterminada de `IObjectModelValidator` en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="7a894-470">Es posible que todavía vea errores de estado del modelo originados en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="7a894-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="7a894-471">Validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-471">Client-side validation</span></span>

<span data-ttu-id="7a894-472">La validación del lado cliente impide realizar el envío hasta que el formulario sea válido.</span><span class="sxs-lookup"><span data-stu-id="7a894-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="7a894-473">El botón Enviar ejecuta JavaScript para enviar el formulario o mostrar mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="7a894-474">La validación del lado cliente evita un recorrido de ida y vuelta innecesario en el servidor cuando hay errores de entrada en un formulario.</span><span class="sxs-lookup"><span data-stu-id="7a894-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="7a894-475">Las siguientes referencias de script de *_Layout.cshtml* y *_ValidationScriptsPartial.cshtml* admiten la validación del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="7a894-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="7a894-476">El script [Validación discreta de jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) es una biblioteca front-end personalizada de Microsoft que se basa en el conocido complemento [Validación de jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="7a894-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="7a894-477">Si no usa Validación discreta de jQuery, deberá codificar la misma lógica de validación dos veces: una vez en los atributos de validación del lado servidor en las propiedades del modelo y luego en los scripts del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="7a894-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="7a894-478">En su lugar, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan los atributos de validación y escriben metadatos de las propiedades del modelo para representar atributos `data-` HTML 5 para los elementos de formulario que necesitan validación.</span><span class="sxs-lookup"><span data-stu-id="7a894-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="7a894-479">Validación discreta de jQuery analiza los atributos `data-` y pasa la lógica a jQuery Validate. De este modo, la lógica de validación del lado servidor se "copia" de manera eficaz en el cliente.</span><span class="sxs-lookup"><span data-stu-id="7a894-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="7a894-480">Puede mostrar errores de validación en el cliente mediante el uso de asistentes de etiquetas, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="7a894-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="7a894-481">Los anteriores asistentes de etiquetas representan el siguiente código HTML.</span><span class="sxs-lookup"><span data-stu-id="7a894-481">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="7a894-482">Tenga en cuenta que los atributos `data-` en los resultados HTML corresponden a los atributos de validación para la propiedad `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="7a894-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="7a894-483">El atributo `data-val-required` contiene un mensaje de error que se muestra si el usuario no rellena el campo de fecha de estreno.</span><span class="sxs-lookup"><span data-stu-id="7a894-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="7a894-484">la validación discreta de jQuery pasa este valor al método de jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) , que después muestra ese mensaje en el **\<span>** elemento correspondiente.</span><span class="sxs-lookup"><span data-stu-id="7a894-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="7a894-485">La validación del tipo de datos se basa en el tipo .NET de una propiedad, a menos que lo reemplace un atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="7a894-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="7a894-486">Los exploradores tienen sus propios mensajes de error de predeterminados, pero el paquete de Validación discreta de jQuery Validate puede invalidar esos mensajes.</span><span class="sxs-lookup"><span data-stu-id="7a894-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="7a894-487">Los atributos y las subclases `[DataType]`, como `[EmailAddress]`, permiten especificar el mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="7a894-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="7a894-488">Agregar validación a formularios dinámicos</span><span class="sxs-lookup"><span data-stu-id="7a894-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="7a894-489">Validación discreta de jQuery pasa los parámetros y la lógica de validación a jQuery Validate cuando la página se carga por primera vez.</span><span class="sxs-lookup"><span data-stu-id="7a894-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="7a894-490">Por lo tanto, la validación no funciona automáticamente en los formularios generados dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="7a894-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="7a894-491">Para habilitar la validación, hay que indicarle a Validación discreta de jQuery que analice el formulario dinámico inmediatamente después de su creación.</span><span class="sxs-lookup"><span data-stu-id="7a894-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="7a894-492">Por ejemplo, en el código siguiente se configura la validación del lado cliente en un formulario agregado mediante AJAX.</span><span class="sxs-lookup"><span data-stu-id="7a894-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="7a894-493">El método `$.validator.unobtrusive.parse()` acepta un selector de jQuery para su único argumento.</span><span class="sxs-lookup"><span data-stu-id="7a894-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="7a894-494">Este método indica a Validación discreta de jQuery que analice los atributos `data-` de formularios dentro de ese selector.</span><span class="sxs-lookup"><span data-stu-id="7a894-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="7a894-495">Después, los valores de estos atributos se pasan al complemento de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="7a894-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="7a894-496">Agregar validación a controles dinámicos</span><span class="sxs-lookup"><span data-stu-id="7a894-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="7a894-497">El método `$.validator.unobtrusive.parse()` funciona en todo el formulario, no en los controles individuales generados dinámicamente, como `<input>` y `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="7a894-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="7a894-498">Para volver a analizar el formulario, quite los datos de validación que se agregaron cuando el formulario se analizó anteriormente, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="7a894-499">Validación del lado cliente personalizada</span><span class="sxs-lookup"><span data-stu-id="7a894-499">Custom client-side validation</span></span>

<span data-ttu-id="7a894-500">Para personalizar la validación del lado cliente, es necesario generar atributos HTML `data-` que funcionen con un adaptador personalizado de jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="7a894-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="7a894-501">El siguiente ejemplo de código de adaptador se escribió para los atributos `ClassicMovie` y `ClassicMovie2` que se introdujeron anteriormente en este artículo:</span><span class="sxs-lookup"><span data-stu-id="7a894-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="7a894-502">Para obtener información sobre cómo escribir adaptadores, vea la [documentación de jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="7a894-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="7a894-503">El uso de un adaptador para un campo determinado se desencadena mediante atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="7a894-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="7a894-504">Marcan que el campo está sujeto a validación (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="7a894-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="7a894-505">Identifican un nombre de regla de validación y un texto de mensaje de error (por ejemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="7a894-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="7a894-506">Proporcionan los parámetros adicionales que necesite el validador (por ejemplo, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="7a894-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="7a894-507">En el ejemplo siguiente se muestran los atributos `data-` para el atributo `ClassicMovie` de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="7a894-508">Como se indicó anteriormente, los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los [asistentes de HTML](xref:mvc/views/overview) usan información procedente de los atributos de validación para representar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="7a894-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="7a894-509">Hay dos opciones para escribir código que dé como resultado la creación de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="7a894-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="7a894-510">Puede crear una clase que derive de `AttributeAdapterBase<TAttribute>` y una clase que implemente `IValidationAttributeAdapterProvider` y, después, registrar el atributo y su adaptador en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="7a894-511">Este método sigue el [principio de responsabilidad única](https://wikipedia.org/wiki/Single_responsibility_principle), ya que el código de validación relacionado con servidor y el relacionado con el cliente se encuentran en clases independientes.</span><span class="sxs-lookup"><span data-stu-id="7a894-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="7a894-512">El adaptador también cuenta con una ventaja: debido a que está registrado en la inserción de dependencias, tiene a su disposición otros servicios de la inserción de dependencias si es necesario.</span><span class="sxs-lookup"><span data-stu-id="7a894-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="7a894-513">Puede implementar `IClientModelValidator` en la clase `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7a894-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="7a894-514">Este método es adecuado si el atributo no realiza ninguna validación en el lado servidor y no necesita ningún servicio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="7a894-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="7a894-515">AttributeAdapter para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="7a894-516">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7a894-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="7a894-517">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="7a894-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="7a894-518">Cree una clase de adaptador de atributo para el atributo de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="7a894-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="7a894-519">Derive la clase de [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="7a894-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="7a894-520">Cree un método `AddValidation` que agregue atributos `data-` a la salida representada, tal como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="7a894-521">Cree una clase de proveedor de adaptador que implemente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="7a894-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="7a894-522">En el método `GetAttributeAdapter`, pase el atributo personalizado al constructor del adaptador, como se muestra en este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a894-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="7a894-523">Registre el proveedor del adaptador para la inserción de dependencias en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a894-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="7a894-524">IClientModelValidator para la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="7a894-525">Este método para representar atributos `data-` en HTML es lo que usa el atributo `ClassicMovie2` en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7a894-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="7a894-526">Para agregar la validación de cliente mediante este método:</span><span class="sxs-lookup"><span data-stu-id="7a894-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="7a894-527">En el atributo de validación personalizado, implemente la interfaz `IClientModelValidator` y cree un método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="7a894-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="7a894-528">En el método `AddValidation`, agregue atributos `data-` para la validación, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7a894-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="7a894-529">Deshabilitación de la validación del lado cliente</span><span class="sxs-lookup"><span data-stu-id="7a894-529">Disable client-side validation</span></span>

<span data-ttu-id="7a894-530">El código siguiente deshabilita la validación de cliente en las vistas de MVC:</span><span class="sxs-lookup"><span data-stu-id="7a894-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="7a894-531">Y en Razor las páginas:</span><span class="sxs-lookup"><span data-stu-id="7a894-531">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="7a894-532">Otra opción para deshabilitar la validación de cliente consiste en marcar como comentario la referencia a `_ValidationScriptsPartial` en el archivo *.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7a894-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a894-533">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7a894-533">Additional resources</span></span>

* [<span data-ttu-id="7a894-534">Espacio de nombres System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="7a894-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="7a894-535">Enlace de modelos</span><span class="sxs-lookup"><span data-stu-id="7a894-535">Model Binding</span></span>](model-binding.md)

::: moniker-end
