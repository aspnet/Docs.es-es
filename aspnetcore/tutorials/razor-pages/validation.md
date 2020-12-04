---
title: Parte 8, adición de validación
author: rick-anderson
description: Parte 8 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f155922c9cb5ea7fdbad0963221ceddd19f4fe60
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419959"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="7609f-103">Parte 8 de la serie de tutoriales sobre Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7609f-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="7609f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7609f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7609f-105">En esta sección se agrega lógica de validación al modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7609f-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="7609f-106">Las reglas de validación se aplican cada vez que un usuario crea o edita una película.</span><span class="sxs-lookup"><span data-stu-id="7609f-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="7609f-107">Validación</span><span class="sxs-lookup"><span data-stu-id="7609f-107">Validation</span></span>

<span data-ttu-id="7609f-108">Un principio clave de desarrollo de software se denomina [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself), por "**D** on't **R** epeat **Y** ourself" (Una vez y solo una).</span><span class="sxs-lookup"><span data-stu-id="7609f-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="7609f-109">Razor Pages fomenta un tipo de desarrollo en el que la funcionalidad se especifica una vez y se refleja en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7609f-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="7609f-110">DRY puede ayudar a:</span><span class="sxs-lookup"><span data-stu-id="7609f-110">DRY can help:</span></span>

* <span data-ttu-id="7609f-111">Reducir la cantidad de código en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="7609f-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="7609f-112">Hacer que el código sea menos propenso a errores y resulte más fácil de probar y mantener.</span><span class="sxs-lookup"><span data-stu-id="7609f-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="7609f-113">La compatibilidad de validación proporcionada por Razor Pages y Entity Framework es un buen ejemplo del principio DRY:</span><span class="sxs-lookup"><span data-stu-id="7609f-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="7609f-114">las reglas de validación se especifican mediante declaración en un lugar, en la clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="7609f-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="7609f-115">Las reglas se aplican en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7609f-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="7609f-116">Adición de reglas de validación al modelo de película</span><span class="sxs-lookup"><span data-stu-id="7609f-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="7609f-117">El espacio de nombres `DataAnnotations` proporciona:</span><span class="sxs-lookup"><span data-stu-id="7609f-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="7609f-118">Un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad.</span><span class="sxs-lookup"><span data-stu-id="7609f-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="7609f-119">Atributos de formato como `[DataType]`, que ayudan a aplicar formato y no proporcionan validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="7609f-120">Actualice la clase `Movie` para aprovechar los atributos de validación integrados `[Required]`, `[StringLength]`, `[RegularExpression]` y `[Range]`.</span><span class="sxs-lookup"><span data-stu-id="7609f-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="7609f-121">Los atributos de validación especifican el comportamiento que se exigirá a las propiedades del modelo al que se aplican:</span><span class="sxs-lookup"><span data-stu-id="7609f-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="7609f-122">Los atributos `[Required]` y `[MinimumLength]` indican que una propiedad debe tener un valor.</span><span class="sxs-lookup"><span data-stu-id="7609f-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="7609f-123">Nada impide que un usuario escriba espacios en blanco para satisfacer esta validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="7609f-124">El atributo `[RegularExpression]` se usa para limitar los caracteres que se pueden escribir.</span><span class="sxs-lookup"><span data-stu-id="7609f-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="7609f-125">En el código anterior `Genre`:</span><span class="sxs-lookup"><span data-stu-id="7609f-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="7609f-126">Solo debe usar letras.</span><span class="sxs-lookup"><span data-stu-id="7609f-126">Must only use letters.</span></span>
  * <span data-ttu-id="7609f-127">La primera letra debe estar en mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="7609f-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="7609f-128">No se permiten espacios en blanco, números ni caracteres especiales.</span><span class="sxs-lookup"><span data-stu-id="7609f-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="7609f-129">`RegularExpression` (`Rating`):</span><span class="sxs-lookup"><span data-stu-id="7609f-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="7609f-130">Requiere que el primer carácter sea una letra mayúscula.</span><span class="sxs-lookup"><span data-stu-id="7609f-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="7609f-131">Permite caracteres especiales y números en los espacios posteriores.</span><span class="sxs-lookup"><span data-stu-id="7609f-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="7609f-132">"PG-13" es válido para una clasificación, pero se produce un error en `Genre`.</span><span class="sxs-lookup"><span data-stu-id="7609f-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="7609f-133">El atributo `[Range]` restringe un valor a un intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="7609f-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="7609f-134">El atributo `[StringLength]` puede establecer la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.</span><span class="sxs-lookup"><span data-stu-id="7609f-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="7609f-135">Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="7609f-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="7609f-136">Las reglas de validación anteriores se utilizan con fines de demostración, no son óptimas para un sistema de producción.</span><span class="sxs-lookup"><span data-stu-id="7609f-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="7609f-137">Por ejemplo, con lo anterior se evita que se escriba una película con solo dos caracteres y no se permiten caracteres especiales en `Genre`.</span><span class="sxs-lookup"><span data-stu-id="7609f-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="7609f-138">La aplicación automática de las reglas de validación por parte de ASP.NET resulta útil por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7609f-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="7609f-139">Ayuda a que la aplicación sea más sólida.</span><span class="sxs-lookup"><span data-stu-id="7609f-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="7609f-140">Reduce las posibilidades de guardar datos no válidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="7609f-141">Interfaz de usuario de error de validación de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7609f-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="7609f-142">Ejecute la aplicación y vaya a Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="7609f-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="7609f-143">Seleccione el vínculo **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7609f-143">Select the **Create New** link.</span></span> <span data-ttu-id="7609f-144">Rellene el formulario con algunos valores no válidos.</span><span class="sxs-lookup"><span data-stu-id="7609f-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="7609f-145">Cuando la validación de cliente de jQuery detecta el error, muestra un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="7609f-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulario de vista de película con varios errores de validación de cliente de jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="7609f-147">Observe cómo el formulario presenta automáticamente un mensaje de error de validación en cada campo que contiene un valor no válido.</span><span class="sxs-lookup"><span data-stu-id="7609f-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="7609f-148">Los errores se aplican al cliente (con JavaScript y jQuery) y al servidor (cuando un usuario tiene JavaScript deshabilitado).</span><span class="sxs-lookup"><span data-stu-id="7609f-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="7609f-149">Una ventaja importante es que **no** se han necesitado cambios de código en las páginas de creación o edición.</span><span class="sxs-lookup"><span data-stu-id="7609f-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="7609f-150">Una vez que se han aplicado al modelo las anotaciones de datos, la interfaz de usuario de validación se ha habilitado.</span><span class="sxs-lookup"><span data-stu-id="7609f-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="7609f-151">Las instancias de Razor Pages creadas en este tutorial han obtenido automáticamente las reglas de validación (por medio de atributos de validación en las propiedades de la clase del modelo `Movie`).</span><span class="sxs-lookup"><span data-stu-id="7609f-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="7609f-152">Al probar la validación en la página de edición, se aplica la misma validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="7609f-153">Los datos del formulario no se publicarán en el servidor hasta que dejen de producirse errores de validación de cliente.</span><span class="sxs-lookup"><span data-stu-id="7609f-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="7609f-154">Compruebe que los datos del formulario no se publican mediante uno o varios de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7609f-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="7609f-155">Coloque un punto de interrupción en el método `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7609f-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="7609f-156">Para enviar el formulario, seleccione **Crear** o **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="7609f-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="7609f-157">El punto de interrupción nunca se alcanza.</span><span class="sxs-lookup"><span data-stu-id="7609f-157">The break point is never hit.</span></span>
* <span data-ttu-id="7609f-158">Use la [herramienta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="7609f-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="7609f-159">Use las herramientas de desarrollo del explorador para supervisar el tráfico de red.</span><span class="sxs-lookup"><span data-stu-id="7609f-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="7609f-160">Validación de servidor</span><span class="sxs-lookup"><span data-stu-id="7609f-160">Server-side validation</span></span>

<span data-ttu-id="7609f-161">Cuando JavaScript está deshabilitado en el explorador, si se envía el formulario con errores, se publica en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7609f-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="7609f-162">Validación de servidor de prueba opcional:</span><span class="sxs-lookup"><span data-stu-id="7609f-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="7609f-163">Deshabilite JavaScript en el explorador.</span><span class="sxs-lookup"><span data-stu-id="7609f-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="7609f-164">JavaScript se puede deshabilitar con las herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="7609f-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="7609f-165">Si no se puede deshabilitar JavaScript en el explorador, pruebe con otro explorador.</span><span class="sxs-lookup"><span data-stu-id="7609f-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="7609f-166">Establezca un punto de interrupción en el método `OnPostAsync` de la página de creación o edición.</span><span class="sxs-lookup"><span data-stu-id="7609f-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="7609f-167">Envíe un formulario con datos no válidos.</span><span class="sxs-lookup"><span data-stu-id="7609f-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="7609f-168">Compruebe que el estado del modelo no es válido:</span><span class="sxs-lookup"><span data-stu-id="7609f-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="7609f-169">Como alternativa, [deshabilite la validación del lado cliente en el servidor](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="7609f-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="7609f-170">El código siguiente muestra una parte de la página *Create.cshtml* a la que se aplicó scaffolding anteriormente en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="7609f-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="7609f-171">Lo usan las páginas de creación y edición para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7609f-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="7609f-172">Mostrar el formulario inicial.</span><span class="sxs-lookup"><span data-stu-id="7609f-172">Display the initial form.</span></span>
* <span data-ttu-id="7609f-173">Volver a mostrar el formulario en caso de error.</span><span class="sxs-lookup"><span data-stu-id="7609f-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="7609f-174">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el cliente.</span><span class="sxs-lookup"><span data-stu-id="7609f-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="7609f-175">El [asistente de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) muestra errores de validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="7609f-176">Para más información, vea [Validación](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="7609f-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="7609f-177">Las páginas de creación y edición no tienen ninguna regla de validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="7609f-178">Las reglas de validación y las cadenas de error solo se especifican en la clase `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7609f-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="7609f-179">Estas reglas de validación se aplican automáticamente a las instancias de Razor Pages que editan el modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7609f-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="7609f-180">Cuando es necesario modificar la lógica de validación, se hace únicamente en el modelo.</span><span class="sxs-lookup"><span data-stu-id="7609f-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="7609f-181">La validación se aplica de forma coherente en toda la aplicación (la lógica de validación se define en un solo lugar).</span><span class="sxs-lookup"><span data-stu-id="7609f-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="7609f-182">La validación en un solo lugar ayuda a mantener limpio el código y facilita su mantenimiento y actualización.</span><span class="sxs-lookup"><span data-stu-id="7609f-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="7609f-183">Uso de atributos DataType</span><span class="sxs-lookup"><span data-stu-id="7609f-183">Use DataType Attributes</span></span>

<span data-ttu-id="7609f-184">Examine la clase `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7609f-184">Examine the `Movie` class.</span></span> <span data-ttu-id="7609f-185">El espacio de nombres `System.ComponentModel.DataAnnotations` proporciona atributos de formato además del conjunto integrado de atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="7609f-186">El atributo `[DataType]` se aplica a las propiedades `ReleaseDate` y `Price`.</span><span class="sxs-lookup"><span data-stu-id="7609f-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="7609f-187">Los atributos `[DataType]` proporcionan:</span><span class="sxs-lookup"><span data-stu-id="7609f-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="7609f-188">Sugerencias para que el motor de vista aplique formato a los datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="7609f-189">Atributos como `<a>` para las direcciones URL y `<a href="mailto:EmailAddress.com">` para el correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="7609f-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="7609f-190">Use el atributo `[RegularExpression]` para validar el formato de los datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="7609f-191">El atributo `[DataType]` se usa para especificar un tipo de datos más específico que el tipo intrínseco de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="7609f-192">Los atributos `[DataType]` no son atributos de validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="7609f-193">En la aplicación de ejemplo solo se muestra la fecha, sin hora.</span><span class="sxs-lookup"><span data-stu-id="7609f-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="7609f-194">La enumeración `DataType` proporciona muchos tipos de datos, como `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, etc.</span><span class="sxs-lookup"><span data-stu-id="7609f-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="7609f-195">Los atributos `[DataType]`:</span><span class="sxs-lookup"><span data-stu-id="7609f-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="7609f-196">Pueden permitir que la aplicación proporcione automáticamente características específicas del tipo.</span><span class="sxs-lookup"><span data-stu-id="7609f-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="7609f-197">Por ejemplo, se puede crear un vínculo `mailto:` para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7609f-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="7609f-198">Pueden proporcionar un selector de fecha `DataType.Date` en exploradores compatibles con HTML5.</span><span class="sxs-lookup"><span data-stu-id="7609f-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="7609f-199">Emiten atributos HTML 5 `data-` (se pronuncia "datos dash") para su uso por parte de los exploradores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="7609f-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="7609f-200">**No** proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="7609f-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="7609f-201">`DataType.Date` no especifica el formato de la fecha que se muestra.</span><span class="sxs-lookup"><span data-stu-id="7609f-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="7609f-202">De manera predeterminada, el campo de datos se muestra según los formatos predeterminados basados en el elemento `CultureInfo` del servidor.</span><span class="sxs-lookup"><span data-stu-id="7609f-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="7609f-203">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` es necesaria para que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="7609f-204">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="7609f-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="7609f-205">El atributo `[DisplayFormat]` se usa para especificar el formato de fecha de forma explícita:</span><span class="sxs-lookup"><span data-stu-id="7609f-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="7609f-206">El valor `ApplyFormatInEditMode` especifica que el formato se aplicará cuando el valor se muestra para su edición.</span><span class="sxs-lookup"><span data-stu-id="7609f-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="7609f-207">Es posible que no se desee ese comportamiento para algunos campos.</span><span class="sxs-lookup"><span data-stu-id="7609f-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="7609f-208">Por ejemplo, en los valores de moneda, probablemente no quiera el símbolo de moneda en la interfaz de usuario de edición.</span><span class="sxs-lookup"><span data-stu-id="7609f-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="7609f-209">El atributo `[DisplayFormat]` puede usarse por sí mismo, pero normalmente es buena idea usar el atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="7609f-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="7609f-210">El atributo `[DataType]` transmite la semántica de los datos en lugar de cómo se representan en una pantalla.</span><span class="sxs-lookup"><span data-stu-id="7609f-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="7609f-211">El atributo `[DataType]` proporciona las siguientes ventajas que no están disponibles en `[DisplayFormat]`:</span><span class="sxs-lookup"><span data-stu-id="7609f-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="7609f-212">El explorador puede habilitar características de HTML5 (por ejemplo, mostrar un control de calendario, el símbolo de moneda adecuado según la configuración regional, vínculos de correo electrónico, etc.).</span><span class="sxs-lookup"><span data-stu-id="7609f-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="7609f-213">De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.</span><span class="sxs-lookup"><span data-stu-id="7609f-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="7609f-214">El atributo `[DataType]` puede habilitar el marco de trabajo de ASP.NET Core para elegir la plantilla de campo correcta a fin de presentar los datos.</span><span class="sxs-lookup"><span data-stu-id="7609f-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="7609f-215">`DisplayFormat`, si se emplea por sí mismo, usa la plantilla de cadena.</span><span class="sxs-lookup"><span data-stu-id="7609f-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="7609f-216">**Nota:** La validación de jQuery no funciona con el atributo `[Range]` ni `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="7609f-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="7609f-217">Por ejemplo, el código siguiente siempre muestra un error de validación de cliente, incluso cuando la fecha está en el intervalo especificado:</span><span class="sxs-lookup"><span data-stu-id="7609f-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="7609f-218">Es un procedimiento recomendado evitar la compilación de fechas fijas en los modelos, por lo que no se recomienda usar el atributo `[Range]` y `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="7609f-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="7609f-219">Utilice [Configuración](xref:fundamentals/configuration/index) para los intervalos de fechas y otros valores que están sujetos a cambios frecuentes en lugar de especificarlos en el código.</span><span class="sxs-lookup"><span data-stu-id="7609f-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="7609f-220">El código siguiente muestra la combinación de atributos en una línea:</span><span class="sxs-lookup"><span data-stu-id="7609f-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="7609f-221">En [Introducción a Razor Pages y EF Core](xref:data/ef-rp/intro) se muestran operaciones avanzadas de EF Core con Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7609f-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7609f-222">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="7609f-222">Apply migrations</span></span>

<span data-ttu-id="7609f-223">Las anotaciones DataAnnotations aplicadas a la clase cambian el esquema.</span><span class="sxs-lookup"><span data-stu-id="7609f-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="7609f-224">Por ejemplo, las DataAnnotations aplicadas al campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="7609f-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="7609f-225">Limita los caracteres a 60.</span><span class="sxs-lookup"><span data-stu-id="7609f-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="7609f-226">No permite un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="7609f-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7609f-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7609f-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7609f-228">La tabla `Movie` tiene actualmente el esquema siguiente:</span><span class="sxs-lookup"><span data-stu-id="7609f-228">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="7609f-229">Los cambios en el esquema anterior no hacen que EF genere una excepción.</span><span class="sxs-lookup"><span data-stu-id="7609f-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="7609f-230">Sin embargo, cree una migración para que el esquema sea coherente con el modelo.</span><span class="sxs-lookup"><span data-stu-id="7609f-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="7609f-231">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="7609f-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="7609f-232">En PCM, escriba los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="7609f-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="7609f-233">`Update-Database` ejecuta los métodos `Up` de la clase `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="7609f-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="7609f-234">Examine el método `Up`:</span><span class="sxs-lookup"><span data-stu-id="7609f-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="7609f-235">La tabla `Movie` actualizada tiene el esquema siguiente:</span><span class="sxs-lookup"><span data-stu-id="7609f-235">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7609f-236">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7609f-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7609f-237">No se requieren migraciones para SQLite.</span><span class="sxs-lookup"><span data-stu-id="7609f-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="7609f-238">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="7609f-238">Publish to Azure</span></span>

<span data-ttu-id="7609f-239">Para obtener información sobre la implementación en Azure, consulte [Tutorial: Compilación de una aplicación ASP.NET Core en Azure con SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="7609f-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="7609f-240">Gracias por finalizar esta introducción sobre Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7609f-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="7609f-241">[Primeros pasos con Razor Pages y EF Core](xref:data/ef-rp/intro) es una excelente continuación para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="7609f-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7609f-242">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7609f-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="7609f-243">Anterior: Adición de un nuevo campo</span><span class="sxs-lookup"><span data-stu-id="7609f-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
