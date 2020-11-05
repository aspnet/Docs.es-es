---
title: 'Parte 8. Adición de validación a una instancia de :::no-loc(Razor)::: Pages de ASP.NET Core'
author: rick-anderson
description: 'Parte 8 de la serie de tutoriales sobre :::no-loc(Razor)::: Pages.'
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 991a0f29c0edc5a220dfde69bd22dc4ed758394d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060734"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="17cd8-103">Parte 8. Adición de validación a una instancia de :::no-loc(Razor)::: Pages de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17cd8-103">Part 8, add validation to an ASP.NET Core :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="17cd8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="17cd8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="17cd8-105">En esta sección se agrega lógica de validación al modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="17cd8-106">Las reglas de validación se aplican cada vez que un usuario crea o edita una película.</span><span class="sxs-lookup"><span data-stu-id="17cd8-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="17cd8-107">Validación</span><span class="sxs-lookup"><span data-stu-id="17cd8-107">Validation</span></span>

<span data-ttu-id="17cd8-108">Un principio clave de desarrollo de software se denomina [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself), por " **D** on't **R** epeat **Y** ourself" (Una vez y solo una).</span><span class="sxs-lookup"><span data-stu-id="17cd8-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="17cd8-109">:::no-loc(Razor)::: Pages fomenta un tipo de desarrollo en el que la funcionalidad se especifica una vez y se refleja en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-109">:::no-loc(Razor)::: Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="17cd8-110">DRY puede ayudar a:</span><span class="sxs-lookup"><span data-stu-id="17cd8-110">DRY can help:</span></span>

* <span data-ttu-id="17cd8-111">Reducir la cantidad de código en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="17cd8-112">Hacer que el código sea menos propenso a errores y resulte más fácil de probar y mantener.</span><span class="sxs-lookup"><span data-stu-id="17cd8-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="17cd8-113">La compatibilidad de validación proporcionada por :::no-loc(Razor)::: Pages y Entity Framework es un buen ejemplo del principio DRY.</span><span class="sxs-lookup"><span data-stu-id="17cd8-113">The validation support provided by :::no-loc(Razor)::: Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="17cd8-114">Las reglas de validación se especifican mediante declaración en un solo lugar (en la clase del modelo) y se aplican en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="17cd8-115">Adición de reglas de validación al modelo de película</span><span class="sxs-lookup"><span data-stu-id="17cd8-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="17cd8-116">El espacio de nombres DataAnnotations proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="17cd8-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="17cd8-117">DataAnnotations también contiene atributos de formato como `DataType` que ayudan a aplicar formato y no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="17cd8-118">Actualice la clase `Movie` para aprovechar los atributos de validación integrados `Required`, `StringLength`, `RegularExpression` y `Range`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="17cd8-119">Los atributos de validación especifican el comportamiento que quiere aplicar en las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="17cd8-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="17cd8-120">Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="17cd8-121">El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="17cd8-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="17cd8-122">En el código anterior, "Género":</span><span class="sxs-lookup"><span data-stu-id="17cd8-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="17cd8-123">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="17cd8-123">Must only use letters.</span></span>
  * <span data-ttu-id="17cd8-124">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="17cd8-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="17cd8-125">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="17cd8-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="17cd8-126">La "Clasificación" de `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="17cd8-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="17cd8-127">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="17cd8-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="17cd8-128">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="17cd8-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="17cd8-129">"PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".</span><span class="sxs-lookup"><span data-stu-id="17cd8-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="17cd8-130">El atributo `Range` restringe un valor a un intervalo determinado.</span><span class="sxs-lookup"><span data-stu-id="17cd8-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="17cd8-131">El atributo `StringLength` permite establecer la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="17cd8-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="17cd8-132">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="17cd8-133">El que ASP.NET Core aplique automáticamente las reglas de validación ayuda a que su aplicación sea más sólida.</span><span class="sxs-lookup"><span data-stu-id="17cd8-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="17cd8-134">También nos permite asegurarnos de que todo se valida y que no nos dejamos ningún dato incorrecto en la base de datos accidentalmente.</span><span class="sxs-lookup"><span data-stu-id="17cd8-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="17cd8-135">Interfaz de usuario de error de validación de :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="17cd8-135">Validation Error UI in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="17cd8-136">Ejecute la aplicación y vaya a Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="17cd8-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="17cd8-137">Seleccione el vínculo **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="17cd8-137">Select the **Create New** link.</span></span> <span data-ttu-id="17cd8-138">Rellene el formulario con algunos valores no válidos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="17cd8-139">Cuando la validación de cliente de jQuery detecta el error, muestra un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="17cd8-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulario de vista de película con varios errores de validación de cliente de jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="17cd8-141">Observe cómo el formulario presenta automáticamente un mensaje de error de validación en cada campo que contiene un valor no válido.</span><span class="sxs-lookup"><span data-stu-id="17cd8-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="17cd8-142">Los errores se aplican al cliente (con JavaScript y jQuery) y al servidor (cuando un usuario tiene JavaScript deshabilitado).</span><span class="sxs-lookup"><span data-stu-id="17cd8-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="17cd8-143">Una ventaja importante es que **no** se han necesitado cambios de código en las páginas de creación o edición.</span><span class="sxs-lookup"><span data-stu-id="17cd8-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="17cd8-144">Una vez que DataAnnotations se ha aplicado al modelo, la interfaz de usuario de validación se ha habilitado.</span><span class="sxs-lookup"><span data-stu-id="17cd8-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="17cd8-145">Las instancias de :::no-loc(Razor)::: Pages creadas en este tutorial han obtenido automáticamente las reglas de validación (por medio de atributos de validación en las propiedades de la clase del modelo `Movie`).</span><span class="sxs-lookup"><span data-stu-id="17cd8-145">The :::no-loc(Razor)::: Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="17cd8-146">Al probar la validación en la página de edición, se aplica la misma validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="17cd8-147">Los datos del formulario no se publicarán en el servidor hasta que dejen de producirse errores de validación de cliente.</span><span class="sxs-lookup"><span data-stu-id="17cd8-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="17cd8-148">Compruebe que los datos del formulario no se publican mediante uno o varios de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="17cd8-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="17cd8-149">Coloque un punto de interrupción en el método `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="17cd8-150">Envíe el formulario (seleccione **Crear** o **Guardar** ).</span><span class="sxs-lookup"><span data-stu-id="17cd8-150">Submit the form (select **Create** or **Save** ).</span></span> <span data-ttu-id="17cd8-151">El punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="17cd8-151">The break point is never hit.</span></span>
* <span data-ttu-id="17cd8-152">Use la [herramienta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="17cd8-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="17cd8-153">Use las herramientas de desarrollo del explorador para supervisar el tráfico de red.</span><span class="sxs-lookup"><span data-stu-id="17cd8-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="17cd8-154">Validación de servidor</span><span class="sxs-lookup"><span data-stu-id="17cd8-154">Server-side validation</span></span>

<span data-ttu-id="17cd8-155">Cuando JavaScript está deshabilitado en el explorador, si se envía el formulario con errores, se publica en el servidor.</span><span class="sxs-lookup"><span data-stu-id="17cd8-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="17cd8-156">Validación de servidor de prueba opcional:</span><span class="sxs-lookup"><span data-stu-id="17cd8-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="17cd8-157">Deshabilite JavaScript en el explorador.</span><span class="sxs-lookup"><span data-stu-id="17cd8-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="17cd8-158">Puede deshabilitar JavaScript con las herramientas para el desarrollador del explorador.</span><span class="sxs-lookup"><span data-stu-id="17cd8-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="17cd8-159">Si no se puede deshabilitar JavaScript en el explorador, pruebe con otro explorador.</span><span class="sxs-lookup"><span data-stu-id="17cd8-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="17cd8-160">Establezca un punto de interrupción en el método `OnPostAsync` de la página de creación o edición.</span><span class="sxs-lookup"><span data-stu-id="17cd8-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="17cd8-161">Envíe un formulario con datos no válidos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="17cd8-162">Compruebe que el estado del modelo no es válido:</span><span class="sxs-lookup"><span data-stu-id="17cd8-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="17cd8-163">Como alternativa, puede [deshabilitar la validación del lado cliente en el servidor](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="17cd8-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="17cd8-164">El código siguiente muestra una parte de la página *Create.cshtml* a la que se aplicó scaffolding anteriormente en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="17cd8-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="17cd8-165">Las páginas de creación y edición la usan para mostrar el formulario inicial y para volver a mostrar el formulario en caso de error.</span><span class="sxs-lookup"><span data-stu-id="17cd8-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="17cd8-166">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el cliente.</span><span class="sxs-lookup"><span data-stu-id="17cd8-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="17cd8-167">El [asistente de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) muestra errores de validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="17cd8-168">Para más información, vea [Validación](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="17cd8-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="17cd8-169">Las páginas de creación y edición no tienen ninguna regla de validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="17cd8-170">Las reglas de validación y las cadenas de error solo se especifican en la clase `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="17cd8-171">Estas reglas de validación se aplican automáticamente a las instancias de :::no-loc(Razor)::: Pages que editan el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-171">These validation rules are automatically applied to :::no-loc(Razor)::: Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="17cd8-172">Cuando es necesario modificar la lógica de validación, se hace únicamente en el modelo.</span><span class="sxs-lookup"><span data-stu-id="17cd8-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="17cd8-173">La validación se aplica de forma coherente en toda la aplicación (la lógica de validación se define en un solo lugar).</span><span class="sxs-lookup"><span data-stu-id="17cd8-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="17cd8-174">La validación en un solo lugar ayuda a mantener limpio el código y facilita su mantenimiento y actualización.</span><span class="sxs-lookup"><span data-stu-id="17cd8-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="17cd8-175">Uso de atributos DataType</span><span class="sxs-lookup"><span data-stu-id="17cd8-175">Using DataType Attributes</span></span>

<span data-ttu-id="17cd8-176">Examine la clase `Movie`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-176">Examine the `Movie` class.</span></span> <span data-ttu-id="17cd8-177">El espacio de nombres `System.ComponentModel.DataAnnotations` proporciona atributos de formato además del conjunto integrado de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="17cd8-178">El atributo `DataType` se aplica a las propiedades `ReleaseDate` y `Price`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="17cd8-179">Los atributos `DataType` solo proporcionan sugerencias para que el motor de vista aplique formato a los datos (y ofrece atributos como `<a>` para las direcciones URL y `<a href="mailto:EmailAddress.com">` para el correo electrónico).</span><span class="sxs-lookup"><span data-stu-id="17cd8-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="17cd8-180">Use el atributo `RegularExpression` para validar el formato de los datos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="17cd8-181">El atributo `DataType` se usa para especificar un tipo de datos más específico que el tipo intrínseco de base de datos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="17cd8-182">Los atributos `DataType` no son atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="17cd8-183">En la aplicación de ejemplo solo se muestra la fecha, sin hora.</span><span class="sxs-lookup"><span data-stu-id="17cd8-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="17cd8-184">La enumeración `DataType` proporciona muchos tipos de datos, como Date, Time, PhoneNumber, Currency, EmailAddress, etc.</span><span class="sxs-lookup"><span data-stu-id="17cd8-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="17cd8-185">El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo.</span><span class="sxs-lookup"><span data-stu-id="17cd8-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="17cd8-186">Por ejemplo, se puede crear un vínculo `mailto:` para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="17cd8-187">Se puede proporcionar un selector de fecha para `DataType.Date` en exploradores compatibles con HTML5.</span><span class="sxs-lookup"><span data-stu-id="17cd8-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="17cd8-188">Los atributos `DataType` emiten atributos HTML 5 `data-` (se pronuncia "datos dash") para su uso por parte de los exploradores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="17cd8-188">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="17cd8-189">Los atributos `DataType`**no** proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="17cd8-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="17cd8-190">`DataType.Date` no especifica el formato de la fecha que se muestra.</span><span class="sxs-lookup"><span data-stu-id="17cd8-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="17cd8-191">De manera predeterminada, el campo de datos se muestra según los formatos predeterminados basados en el elemento `CultureInfo` del servidor.</span><span class="sxs-lookup"><span data-stu-id="17cd8-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="17cd8-192">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` es necesaria para que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="17cd8-193">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="17cd8-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="17cd8-194">El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita:</span><span class="sxs-lookup"><span data-stu-id="17cd8-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="17cd8-195">El valor `ApplyFormatInEditMode` especifica que el formato se debe aplicar cuando el valor se muestra para su edición.</span><span class="sxs-lookup"><span data-stu-id="17cd8-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="17cd8-196">Es posible que no quiera ese comportamiento para algunos campos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="17cd8-197">Por ejemplo, en los valores de moneda, probablemente no quiera el símbolo de moneda en la interfaz de usuario de edición.</span><span class="sxs-lookup"><span data-stu-id="17cd8-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="17cd8-198">El atributo `DisplayFormat` puede usarse por sí mismo, pero normalmente es buena idea usar el atributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="17cd8-199">El atributo `DataType` transmite la semántica de los datos en contraposición a cómo se representan en una pantalla y ofrece las siguientes ventajas que no proporciona DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="17cd8-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="17cd8-200">El explorador puede habilitar características de HTML5 (por ejemplo, mostrar un control de calendario, el símbolo de moneda adecuado según la configuración regional, vínculos de correo electrónico, etc.).</span><span class="sxs-lookup"><span data-stu-id="17cd8-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="17cd8-201">De forma predeterminada, el explorador presenta los datos con el formato correcto en función de la configuración regional.</span><span class="sxs-lookup"><span data-stu-id="17cd8-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="17cd8-202">El atributo `DataType` puede habilitar el marco de trabajo de ASP.NET Core para elegir la plantilla de campo correcta a fin de presentar los datos.</span><span class="sxs-lookup"><span data-stu-id="17cd8-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="17cd8-203">`DisplayFormat`, si se emplea por sí mismo, usa la plantilla de cadena.</span><span class="sxs-lookup"><span data-stu-id="17cd8-203">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="17cd8-204">**Nota:** La validación de jQuery no funciona con el atributo `Range` ni `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-204">**Note:** jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="17cd8-205">Por ejemplo, el código siguiente siempre muestra un error de validación de cliente, incluso cuando la fecha está en el intervalo especificado:</span><span class="sxs-lookup"><span data-stu-id="17cd8-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="17cd8-206">Por lo general no se recomienda compilar fechas fijas en los modelos, así que se desaconseja el empleo del atributo `Range` y `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="17cd8-207">El código siguiente muestra la combinación de atributos en una línea:</span><span class="sxs-lookup"><span data-stu-id="17cd8-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="17cd8-208">En [Introducción a :::no-loc(Razor)::: Pages y EF Core](xref:data/ef-rp/intro) se muestran operaciones avanzadas de EF Core con :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="17cd8-208">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with :::no-loc(Razor)::: Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="17cd8-209">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="17cd8-209">Apply migrations</span></span>

<span data-ttu-id="17cd8-210">Las anotaciones DataAnnotations aplicadas a la clase cambian el esquema.</span><span class="sxs-lookup"><span data-stu-id="17cd8-210">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="17cd8-211">Por ejemplo, las DataAnnotations aplicadas al campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="17cd8-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="17cd8-212">Limita los caracteres a 60.</span><span class="sxs-lookup"><span data-stu-id="17cd8-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="17cd8-213">No permite un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17cd8-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17cd8-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17cd8-215">La tabla `Movie` tiene actualmente el esquema siguiente:</span><span class="sxs-lookup"><span data-stu-id="17cd8-215">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="17cd8-216">Los cambios en el esquema anterior no hacen que EF genere una excepción.</span><span class="sxs-lookup"><span data-stu-id="17cd8-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="17cd8-217">Sin embargo, cree una migración para que el esquema sea coherente con el modelo.</span><span class="sxs-lookup"><span data-stu-id="17cd8-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="17cd8-218">En el menú **Herramientas** , seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="17cd8-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="17cd8-219">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="17cd8-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="17cd8-220">`Update-Database` ejecuta los métodos `Up` de la clase `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="17cd8-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="17cd8-221">Examine el método `Up`:</span><span class="sxs-lookup"><span data-stu-id="17cd8-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="17cd8-222">La tabla `Movie` actualizada tiene el esquema siguiente:</span><span class="sxs-lookup"><span data-stu-id="17cd8-222">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="17cd8-223">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="17cd8-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="17cd8-224">No se requieren migraciones para SQLite.</span><span class="sxs-lookup"><span data-stu-id="17cd8-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="17cd8-225">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="17cd8-225">Publish to Azure</span></span>

<span data-ttu-id="17cd8-226">Para obtener información sobre la implementación en Azure, consulte [Tutorial: Compilación de una aplicación ASP.NET Core en Azure con SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="17cd8-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="17cd8-227">Gracias por finalizar esta introducción sobre :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="17cd8-227">Thanks for completing this introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="17cd8-228">[Primeros pasos con :::no-loc(Razor)::: Pages y EF Core](xref:data/ef-rp/intro) es una excelente continuación para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="17cd8-228">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17cd8-229">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17cd8-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="17cd8-230">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="17cd8-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> <span data-ttu-id="17cd8-231">[Anterior: ](xref:tutorials/razor-pages/new-field)Adición de un nuevo campo</span><span class="sxs-lookup"><span data-stu-id="17cd8-231">[Previous: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>
