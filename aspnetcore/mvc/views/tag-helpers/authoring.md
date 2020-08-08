---
title: Crear asistentes de etiquetas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear asistentes de etiquetas en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: d40b37a4ca8acf57b662124597a8ebc3b90bffd2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018173"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="bcf17-103">Crear asistentes de etiquetas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bcf17-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="bcf17-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bcf17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bcf17-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bcf17-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="bcf17-106">Introducción a los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="bcf17-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="bcf17-107">En este tutorial se proporciona una introducción a la programación de asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="bcf17-108">En [Introducción a los asistentes de etiquetas](intro.md) se describen las ventajas que proporcionan los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="bcf17-109">Un asistente de etiquetas es una clase que implementa la interfaz `ITagHelper`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="bcf17-110">A pesar de ello, cuando se crea un asistente de etiquetas, normalmente se deriva de `TagHelper`, lo que da acceso al método `Process`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="bcf17-111">Cree un proyecto de ASP.NET Core denominado **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="bcf17-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="bcf17-112">No necesita autenticación para este proyecto.</span><span class="sxs-lookup"><span data-stu-id="bcf17-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="bcf17-113">Cree una carpeta para almacenar los asistentes de etiquetas denominada *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="bcf17-114">La carpeta *TagHelpers\*\*no* es necesaria, pero es una convención razonable.</span><span class="sxs-lookup"><span data-stu-id="bcf17-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="bcf17-115">Ahora vamos a empezar a escribir algunos asistentes de etiquetas simples.</span><span class="sxs-lookup"><span data-stu-id="bcf17-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="bcf17-116">Asistente de etiquetas mínima</span><span class="sxs-lookup"><span data-stu-id="bcf17-116">A minimal Tag Helper</span></span>

<span data-ttu-id="bcf17-117">En esta sección, escribirá un asistente de etiquetas que actualice una etiqueta de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="bcf17-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="bcf17-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bcf17-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="bcf17-119">El servidor usará nuestro asistente de etiquetas de correo electrónico para convertir ese marcado en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="bcf17-120">Es decir, una etiqueta delimitadora lo convierte en un vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="bcf17-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="bcf17-121">Tal vez le interese si está escribiendo un motor de blogs y necesita que envíe correos electrónicos a contactos de marketing, soporte técnico y de otro tipo, todos ellos en el mismo dominio.</span><span class="sxs-lookup"><span data-stu-id="bcf17-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="bcf17-122">Agregue la siguiente clase `EmailTagHelper` a la carpeta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="bcf17-123">Los asistentes de etiquetas usan una convención de nomenclatura que tiene como destino los elementos de la clase raíz (menos la parte *TagHelper* del nombre de clase).</span><span class="sxs-lookup"><span data-stu-id="bcf17-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="bcf17-124">En este ejemplo, el nombre de raíz de **EmailTagHelper** es *email*, por lo que se usará la `<email>` etiqueta como destino.</span><span class="sxs-lookup"><span data-stu-id="bcf17-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="bcf17-125">Esta convención de nomenclatura debería funcionar para la mayoría de los asistentes de etiquetas. Más adelante veremos cómo invalidarla.</span><span class="sxs-lookup"><span data-stu-id="bcf17-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="bcf17-126">La clase `EmailTagHelper` deriva de `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="bcf17-127">La clase `TagHelper` proporciona métodos y propiedades para escribir asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="bcf17-128">El método `Process` invalidado controla lo que hace el asistente de etiquetas cuando se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="bcf17-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="bcf17-129">La clase `TagHelper` también proporciona una versión asincrónica (`ProcessAsync`) con los mismos parámetros.</span><span class="sxs-lookup"><span data-stu-id="bcf17-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="bcf17-130">El parámetro de contexto para `Process` (y `ProcessAsync`) contiene información relacionada con la ejecución de la etiqueta HTML actual.</span><span class="sxs-lookup"><span data-stu-id="bcf17-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="bcf17-131">El parámetro de salida para `Process` (y `ProcessAsync`) contiene un elemento HTML con estado que representa el origen original usado para generar una etiqueta y contenido HTML.</span><span class="sxs-lookup"><span data-stu-id="bcf17-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="bcf17-132">El nombre de nuestra clase tiene un sufijo **TagHelper**, que *no* es necesario, pero es una convención recomendada.</span><span class="sxs-lookup"><span data-stu-id="bcf17-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="bcf17-133">Podría declarar la clase de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="bcf17-134">Para que la `EmailTagHelper` clase esté disponible para todas nuestras Razor vistas, agregue la `addTagHelper` Directiva al archivo *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bcf17-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="bcf17-135">El código anterior usa la sintaxis de comodines para especificar que todos los asistentes de etiquetas del ensamblado estarán disponibles.</span><span class="sxs-lookup"><span data-stu-id="bcf17-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="bcf17-136">La primera cadena después de `@addTagHelper` especifica el asistente de etiquetas que se va a cargar (use "\*" para todos los asistentes de etiquetas), mientras que la segunda cadena "AuthoringTagHelpers" especifica el ensamblado en el que se encuentra el asistente de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="bcf17-137">Además, tenga en cuenta que la segunda línea incluye las aplicaciones auxiliares de etiquetas de ASP.NET Core MVC que usan la sintaxis de caracteres comodín (esas aplicaciones auxiliares se describen en [Introducción a las aplicaciones auxiliares de etiquetas](intro.md)). Se trata de la `@addTagHelper` Directiva que hace que la aplicación auxiliar de etiquetas esté disponible para la Razor vista.</span><span class="sxs-lookup"><span data-stu-id="bcf17-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="bcf17-138">Como alternativa, puede proporcionar el nombre completo (FQN) de un asistente de etiquetas como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="bcf17-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="bcf17-139">Para agregar un asistente de etiquetas a una vista con un FQN, agregue primero el FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) y, después, el **nombre del ensamblado** (*AuthoringTagHelpers*, no necesariamente `namespace`).</span><span class="sxs-lookup"><span data-stu-id="bcf17-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="bcf17-140">La mayoría de los desarrolladores prefiere usar la sintaxis de comodines.</span><span class="sxs-lookup"><span data-stu-id="bcf17-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="bcf17-141">En [Introducción a los asistentes de etiquetas](intro.md) se describe en detalle la adición y eliminación de asistentes de etiquetas, la jerarquía y la sintaxis de comodines.</span><span class="sxs-lookup"><span data-stu-id="bcf17-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="bcf17-142">Actualice el marcado del archivo *Views/Home/Contact.cshtml* con estos cambios:</span><span class="sxs-lookup"><span data-stu-id="bcf17-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="bcf17-143">Ejecute la aplicación y use su explorador favorito para ver el código fuente HTML, a fin de comprobar que las etiquetas de correo electrónico se han reemplazado por un marcado delimitador (por ejemplo, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="bcf17-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="bcf17-144">*Support* y *Marketing* se representan como vínculos, pero no tienen un atributo `href` que los haga funcionales.</span><span class="sxs-lookup"><span data-stu-id="bcf17-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="bcf17-145">Esto lo corregiremos en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="bcf17-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="bcf17-146">SetAttribute y SetContent</span><span class="sxs-lookup"><span data-stu-id="bcf17-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="bcf17-147">En esta sección, actualizaremos `EmailTagHelper` para que cree una etiqueta delimitadora válida para correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="bcf17-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="bcf17-148">Lo actualizaremos para tomar información de una Razor vista (en forma de `mail-to` atributo) y usarla en la generación del delimitador.</span><span class="sxs-lookup"><span data-stu-id="bcf17-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="bcf17-149">Actualice la clase `EmailTagHelper` con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="bcf17-150">Los nombres de clase y propiedad con grafía Pascal para los asistentes de etiquetas se convierten a su [grafía kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="bcf17-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="bcf17-151">Por tanto, para usar el atributo `MailTo`, usará su equivalente `<email mail-to="value"/>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="bcf17-152">La última línea establece el contenido completado para nuestro asistente de etiquetas mínimamente funcional.</span><span class="sxs-lookup"><span data-stu-id="bcf17-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="bcf17-153">La línea resaltada muestra la sintaxis para agregar atributos:</span><span class="sxs-lookup"><span data-stu-id="bcf17-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="bcf17-154">Este enfoque funciona para el atributo "href" siempre y cuando no exista actualmente en la colección de atributos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="bcf17-155">También puede usar el método `output.Attributes.Add` para agregar un atributo del asistente de etiquetas al final de la colección de atributos de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="bcf17-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="bcf17-156">Actualice el marcado del archivo *Views/Home/Contact.cshtml* con estos cambios:</span><span class="sxs-lookup"><span data-stu-id="bcf17-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="bcf17-157">Ejecute la aplicación y compruebe que genera los vínculos correctos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="bcf17-158">Si escribe la etiqueta de correo electrónico como de autocierre (`<email mail-to="Rick" />`), la salida final también será de autocierre.</span><span class="sxs-lookup"><span data-stu-id="bcf17-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="bcf17-159">Para habilitar la capacidad de escribir la etiqueta únicamente con una etiqueta de apertura (`<email mail-to="Rick">`), debe marcar la clase con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="bcf17-160">Con un asistente de etiquetas de correo electrónico de autocierre, el resultado sería `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="bcf17-161">Las etiquetas delimitadoras de autocierre no son HTML válido, por lo que no le interesa crear una, pero tal vez le convenga crear un asistente de etiquetas de autocierre.</span><span class="sxs-lookup"><span data-stu-id="bcf17-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="bcf17-162">Los asistentes de etiquetas establecen el tipo de la propiedad `TagMode` después de leer una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="bcf17-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="bcf17-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="bcf17-163">ProcessAsync</span></span>

<span data-ttu-id="bcf17-164">En esta sección, escribiremos un asistente de correo electrónico asincrónico.</span><span class="sxs-lookup"><span data-stu-id="bcf17-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="bcf17-165">Reemplace la clase `EmailTagHelper` por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="bcf17-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="bcf17-166">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="bcf17-166">**Notes:**</span></span>

   * <span data-ttu-id="bcf17-167">Esta versión usa el método `ProcessAsync` asincrónico.</span><span class="sxs-lookup"><span data-stu-id="bcf17-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="bcf17-168">El método `GetChildContentAsync` asincrónico devuelve un valor `Task` que contiene `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="bcf17-169">Use el parámetro `output` para obtener el contenido del elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="bcf17-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="bcf17-170">Realice el cambio siguiente en el archivo *Views/Home/Contact.cshtml* para que el asistente de etiquetas pueda obtener el correo electrónico de destino.</span><span class="sxs-lookup"><span data-stu-id="bcf17-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="bcf17-171">Ejecute la aplicación y compruebe que genera vínculos de correo electrónico válidos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="bcf17-172">RemoveAll, PreContent.SetHtmlContent y PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="bcf17-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="bcf17-173">Agregue la siguiente clase `BoldTagHelper` a la carpeta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="bcf17-174">El atributo `[HtmlTargetElement]` pasa un parámetro de atributo que especifica que todos los elementos HTML que contengan un atributo HTML denominado "bold" coincidirán, y se ejecutará el método de invalidación `Process` de la clase.</span><span class="sxs-lookup"><span data-stu-id="bcf17-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="bcf17-175">En nuestro ejemplo, el método `Process` quita el atributo "bold" y rodea el marcado contenedor con `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="bcf17-176">Dado que no le interesa reemplazar el contenido existente de la etiqueta, debe escribir la etiqueta de apertura `<strong>` con el método `PreContent.SetHtmlContent` y la etiqueta de cierre `</strong>` con el método `PostContent.SetHtmlContent`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="bcf17-177">Modifique la vista *About.cshtml* para que contenga un valor de atributo `bold`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="bcf17-178">A continuación se muestra el código completado.</span><span class="sxs-lookup"><span data-stu-id="bcf17-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="bcf17-179">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bcf17-179">Run the app.</span></span> <span data-ttu-id="bcf17-180">Puede usar el explorador que prefiera para inspeccionar el origen y comprobar el marcado.</span><span class="sxs-lookup"><span data-stu-id="bcf17-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="bcf17-181">El atributo `[HtmlTargetElement]` anterior solo tiene como destino el marcado HTML que proporciona el nombre de atributo "bold".</span><span class="sxs-lookup"><span data-stu-id="bcf17-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="bcf17-182">El asistente de etiquetas no ha modificado el elemento `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="bcf17-183">Convierta en comentario la línea de atributo `[HtmlTargetElement]` y de forma predeterminada tendrá como destino las etiquetas `<bold>`, es decir, el marcado HTML con formato `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="bcf17-184">Recuerde que la convención de nomenclatura predeterminada hará coincidir el nombre de clase **Bold**TagHelper con las etiquetas `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-184">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="bcf17-185">Ejecute la aplicación y compruebe que el asistente de etiquetas procesa la etiqueta `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="bcf17-186">El proceso de decorar una clase con varios atributos `[HtmlTargetElement]` tiene como resultado una operación OR lógica de los destinos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="bcf17-187">Por ejemplo, si se usa el código siguiente, una etiqueta bold o un atributo bold coincidirán.</span><span class="sxs-lookup"><span data-stu-id="bcf17-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="bcf17-188">Cuando se agregan varios atributos a la misma instrucción, el tiempo de ejecución los trata como una operación AND lógica.</span><span class="sxs-lookup"><span data-stu-id="bcf17-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="bcf17-189">Por ejemplo, en el código siguiente, un elemento HTML debe denominarse "bold" con un atributo denominado "bold" (`<bold bold />`) para que coincida.</span><span class="sxs-lookup"><span data-stu-id="bcf17-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="bcf17-190">También puede usar `[HtmlTargetElement]` para cambiar el nombre del elemento de destino.</span><span class="sxs-lookup"><span data-stu-id="bcf17-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="bcf17-191">Por ejemplo, si quiere que `BoldTagHelper` tenga como destino etiquetas `<MyBold>`, use el atributo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="bcf17-192">Pasar un modelo a un asistente de etiquetas</span><span class="sxs-lookup"><span data-stu-id="bcf17-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="bcf17-193">Agregue una carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="bcf17-194">Agregue la clase `WebsiteContext` siguiente a la carpeta *Models*:</span><span class="sxs-lookup"><span data-stu-id="bcf17-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="bcf17-195">Agregue la siguiente clase `WebsiteInformationTagHelper` a la carpeta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="bcf17-196">Como se ha indicado anteriormente, los asistentes de etiquetas convierten las propiedades y nombres de clase de C# con grafía Pascal para asistentes de etiquetas en [grafía kebab](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="bcf17-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="bcf17-197">Por lo tanto, para usar `WebsiteInformationTagHelper` en Razor , escribirá `<website-information />` .</span><span class="sxs-lookup"><span data-stu-id="bcf17-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="bcf17-198">No está identificando de manera explícita el elemento de destino con el atributo `[HtmlTargetElement]`, por lo que el destino será el valor predeterminado de `website-information`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="bcf17-199">Si ha aplicado el atributo siguiente (tenga en cuenta que no tiene grafía kebab, pero coincide con el nombre de clase):</span><span class="sxs-lookup"><span data-stu-id="bcf17-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="bcf17-200">La etiqueta con grafía kebab `<website-information />` no coincidiría.</span><span class="sxs-lookup"><span data-stu-id="bcf17-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="bcf17-201">Si quiere usar el atributo `[HtmlTargetElement]`, debe usar la grafía kebab como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="bcf17-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="bcf17-202">Los elementos que son de autocierre no tienen contenido.</span><span class="sxs-lookup"><span data-stu-id="bcf17-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="bcf17-203">En este ejemplo, el Razor marcado usará una etiqueta de autocierre, pero la aplicación auxiliar de etiquetas creará un elemento [section](https://www.w3.org/TR/html5/sections.html#the-section-element) (que no se cierra automáticamente y está escribiendo contenido dentro del `section` elemento).</span><span class="sxs-lookup"><span data-stu-id="bcf17-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="bcf17-204">Por tanto, debe establecer `TagMode` en `StartTagAndEndTag` para escribir la salida.</span><span class="sxs-lookup"><span data-stu-id="bcf17-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="bcf17-205">Como alternativa, puede convertir en comentario la línea donde se establece `TagMode` y escribir marcado con una etiqueta de cierre.</span><span class="sxs-lookup"><span data-stu-id="bcf17-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="bcf17-206">(Más adelante en este tutorial se proporciona marcado de ejemplo).</span><span class="sxs-lookup"><span data-stu-id="bcf17-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="bcf17-207">El signo de dólar `$` de la línea siguiente usa una [cadena interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="bcf17-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="bcf17-208">Agregue el marcado siguiente a la vista *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="bcf17-209">En el marcado resaltado se muestra la información del sitio web.</span><span class="sxs-lookup"><span data-stu-id="bcf17-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="bcf17-210">En el Razor marcado que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="bcf17-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="bcf17-211">Razorsabe que el `info` atributo es una clase, no una cadena, y desea escribir código de C#.</span><span class="sxs-lookup"><span data-stu-id="bcf17-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="bcf17-212">Todos los atributos de asistentes de etiquetas que no sean una cadena deben escribirse sin el carácter `@`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="bcf17-213">Ejecute la aplicación y vaya a la vista About para ver la información del sitio web.</span><span class="sxs-lookup"><span data-stu-id="bcf17-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="bcf17-214">Puede usar el marcado siguiente con una etiqueta de cierre y quitar la línea con `TagMode.StartTagAndEndTag` del asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="bcf17-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="bcf17-215">Asistente de etiquetas de condición</span><span class="sxs-lookup"><span data-stu-id="bcf17-215">Condition Tag Helper</span></span>

<span data-ttu-id="bcf17-216">El asistente de etiquetas de condición representa la salida cuando se pasa un valor true.</span><span class="sxs-lookup"><span data-stu-id="bcf17-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="bcf17-217">Agregue la siguiente clase `ConditionTagHelper` a la carpeta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="bcf17-218">Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="bcf17-219">Reemplace el método `Index` del controlador `Home` por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="bcf17-220">Ejecute la aplicación y vaya a la página principal.</span><span class="sxs-lookup"><span data-stu-id="bcf17-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="bcf17-221">El marcado del elemento condicional `div` no se representará.</span><span class="sxs-lookup"><span data-stu-id="bcf17-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="bcf17-222">Anexe la cadena de consulta `?approved=true` a la dirección URL (por ejemplo, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="bcf17-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="bcf17-223">`approved` se establece en true y se muestra el marcado condicional.</span><span class="sxs-lookup"><span data-stu-id="bcf17-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="bcf17-224">Use el operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) para especificar el atributo de destino en lugar de especificar una cadena, como hizo con el asistente de etiquetas bold:</span><span class="sxs-lookup"><span data-stu-id="bcf17-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="bcf17-225">El operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) protegerá el código si en algún momento debe refactorizarse (tal vez interese cambiar el nombre a `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="bcf17-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="bcf17-226">Evitar conflictos de asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="bcf17-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="bcf17-227">En esta sección, escribirá un par de asistentes de etiquetas de vinculación automática.</span><span class="sxs-lookup"><span data-stu-id="bcf17-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="bcf17-228">La primera reemplazará el marcado que contiene una dirección URL que empieza con HTTP por una etiqueta delimitadora HTML que contiene la misma dirección URL (y, por tanto, produce un vínculo a la dirección URL).</span><span class="sxs-lookup"><span data-stu-id="bcf17-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="bcf17-229">La segunda hará lo mismo para una dirección URL que empieza con WWW.</span><span class="sxs-lookup"><span data-stu-id="bcf17-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="bcf17-230">Dado que estos dos asistentes están estrechamente relacionados y tal vez las refactorice en el futuro, los guardaremos en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="bcf17-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="bcf17-231">Agregue la siguiente clase `AutoLinkerHttpTagHelper` a la carpeta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="bcf17-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="bcf17-232">La clase `AutoLinkerHttpTagHelper` tiene como destino elementos `p` y usa [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) para crear el delimitador.</span><span class="sxs-lookup"><span data-stu-id="bcf17-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="bcf17-233">Agregue el marcado siguiente al final del archivo *Views/Home/Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bcf17-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="bcf17-234">Ejecute la aplicación y compruebe que el asistente de etiquetas representa el delimitador correctamente.</span><span class="sxs-lookup"><span data-stu-id="bcf17-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="bcf17-235">Actualice la clase `AutoLinker` para que incluya la aplicación auxiliar de etiquetas `AutoLinkerWwwTagHelper` que convertirá el texto www en una etiqueta delimitadora que también contenga el texto www original.</span><span class="sxs-lookup"><span data-stu-id="bcf17-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="bcf17-236">El código actualizado aparece resaltado a continuación:</span><span class="sxs-lookup"><span data-stu-id="bcf17-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="bcf17-237">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bcf17-237">Run the app.</span></span> <span data-ttu-id="bcf17-238">Observe que el texto www se representa como un vínculo, a diferencia del texto HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcf17-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="bcf17-239">Si coloca un punto de interrupción en ambas clases, verá que la clase del asistente de etiquetas HTTP se ejecuta primero.</span><span class="sxs-lookup"><span data-stu-id="bcf17-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="bcf17-240">El problema es que la salida del asistente de etiquetas se almacena en caché y, cuando se ejecuta el asistente de etiquetas WWW, sobrescribe la salida almacenada en caché desdel asistente de etiquetas HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcf17-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="bcf17-241">Más adelante en el tutorial veremos cómo se controla el orden en el que se ejecutan los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="bcf17-242">Corregiremos el código con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="bcf17-243">La primera vez que editó los asistentes de etiquetas de vinculación automática, obtuvo el contenido del destino con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="bcf17-244">Es decir, ha llamado a `GetChildContentAsync` mediante la salida `TagHelperOutput` pasada al método `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="bcf17-245">Como ya se ha indicado, como la salida se almacena en caché, prevalece el último asistente de etiquetas que se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="bcf17-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="bcf17-246">Para corregir el error, ha usado el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="bcf17-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="bcf17-247">El código anterior comprueba si se ha modificado el contenido y, en caso afirmativo, obtiene el contenido del búfer de salida.</span><span class="sxs-lookup"><span data-stu-id="bcf17-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="bcf17-248">Ejecute la aplicación y compruebe que los dos vínculos funcionan según lo previsto.</span><span class="sxs-lookup"><span data-stu-id="bcf17-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="bcf17-249">Aunque podría parecer que nuestro asistente de etiquetas de vinculación automática es correcta y está completa, tiene un pequeño problema.</span><span class="sxs-lookup"><span data-stu-id="bcf17-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="bcf17-250">Si el asistente de etiquetas WWW se ejecuta en primer lugar, los vínculos www no serán correctos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="bcf17-251">Actualice el código mediante la adición de la sobrecarga `Order` para controlar el orden en el que se ejecuta la etiqueta.</span><span class="sxs-lookup"><span data-stu-id="bcf17-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="bcf17-252">La propiedad `Order` determina el orden de ejecución en relación con los demás asistentes de etiquetas que tienen como destino el mismo elemento.</span><span class="sxs-lookup"><span data-stu-id="bcf17-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="bcf17-253">El valor de orden predeterminado es cero, y se ejecutan en primer lugar las instancias con los valores más bajos.</span><span class="sxs-lookup"><span data-stu-id="bcf17-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="bcf17-254">El código anterior garantizará que el asistente de etiquetas HTTP se ejecuta antes que el asistente de etiquetas WWW.</span><span class="sxs-lookup"><span data-stu-id="bcf17-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="bcf17-255">Cambie `Order` a `MaxValue` y compruebe que el marcado generado para la etiqueta WWW es incorrecto.</span><span class="sxs-lookup"><span data-stu-id="bcf17-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="bcf17-256">Inspeccionar y recuperar contenido secundario</span><span class="sxs-lookup"><span data-stu-id="bcf17-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="bcf17-257">Los asistentes de etiquetas proporcionan varias propiedades para recuperar contenido.</span><span class="sxs-lookup"><span data-stu-id="bcf17-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="bcf17-258">El resultado de `GetChildContentAsync` se pueden anexar a `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="bcf17-259">Puede inspeccionar el resultado de `GetChildContentAsync` con `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="bcf17-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="bcf17-260">Si modifica `output.Content`, el cuerpo de TagHelper no se ejecutará ni representará a menos que llame a `GetChildContentAsync`, como en nuestro ejemplo de vinculación automática:</span><span class="sxs-lookup"><span data-stu-id="bcf17-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="bcf17-261">Varias llamadas a `GetChildContentAsync` devuelven el mismo valor y no vuelven a ejecutar el cuerpo de `TagHelper` a menos que se pase un parámetro false que indique que no se use el resultado almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="bcf17-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="bcf17-262">Carga de la vista parcial minimizada TagHelper</span><span class="sxs-lookup"><span data-stu-id="bcf17-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="bcf17-263">En entornos de producción, se puede mejorar el rendimiento cargando vistas parciales minimizadas.</span><span class="sxs-lookup"><span data-stu-id="bcf17-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="bcf17-264">Para aprovechar las ventajas de la vista parcial minimizada en producción, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="bcf17-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="bcf17-265">Cree o configure un proceso de compilación anterior que minimice las vistas parciales.</span><span class="sxs-lookup"><span data-stu-id="bcf17-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="bcf17-266">Utilice el siguiente código para cargar las vistas parciales minimizadas en entornos que no son de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="bcf17-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
