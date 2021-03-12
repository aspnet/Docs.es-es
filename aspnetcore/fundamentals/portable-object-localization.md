---
title: Configurar la localización de objetos portátiles en ASP.NET Core
author: sebastienros
description: En este artículo se presentan los archivos de objeto portátil y se describen los pasos para usarlos en una aplicación ASP.NET Core con el marco de trabajo de Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: ed41657b364c7f845491b3e452db8a4d5c5aa389
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587182"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="4b37c-103">Configurar la localización de objetos portátiles en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b37c-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b37c-104">De [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) y [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="4b37c-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="4b37c-105">En este artículo se describen los pasos para usar archivos de objeto portátil (PO) en una aplicación ASP.NET Core con el marco de trabajo de [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="4b37c-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="4b37c-106">**Nota:** Orchard Core no es un producto de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="4b37c-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="4b37c-107">Por tanto, Microsoft no ofrece soporte técnico para esta característica.</span><span class="sxs-lookup"><span data-stu-id="4b37c-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="4b37c-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b37c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="4b37c-109">¿Qué es un archivo de objeto portátil?</span><span class="sxs-lookup"><span data-stu-id="4b37c-109">What is a PO file?</span></span>

<span data-ttu-id="4b37c-110">Los archivos de objeto portátil se distribuyen como archivos de texto que contienen las cadenas traducidas de un idioma determinado.</span><span class="sxs-lookup"><span data-stu-id="4b37c-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="4b37c-111">Entre las ventajas de usar archivos de objeto portátil en lugar de archivos *.resx* se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="4b37c-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="4b37c-112">Los archivos de objeto portátil admiten la pluralización, a diferencia de los archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="4b37c-113">Los archivos de objeto portátil no se compilan como archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="4b37c-114">Por tanto, no se requieren herramientas ni pasos de compilación especializados.</span><span class="sxs-lookup"><span data-stu-id="4b37c-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="4b37c-115">Los archivos de objeto portátil funcionan bien con herramientas de colaboración de edición en línea.</span><span class="sxs-lookup"><span data-stu-id="4b37c-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="4b37c-116">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="4b37c-116">Example</span></span>

<span data-ttu-id="4b37c-117">Este es un archivo de objeto portátil de ejemplo que contiene la traducción de dos cadenas en francés, incluida una con su forma en plural:</span><span class="sxs-lookup"><span data-stu-id="4b37c-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="4b37c-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="4b37c-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="4b37c-119">En este ejemplo se usa la sintaxis siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="4b37c-120">`#:`: comentario que indica el contexto de la cadena que se va a traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="4b37c-121">La misma cadena se podría traducir de forma diferente según donde se vaya a usar.</span><span class="sxs-lookup"><span data-stu-id="4b37c-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="4b37c-122">`msgid`: la cadena sin traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="4b37c-123">`msgstr`: la cadena traducida.</span><span class="sxs-lookup"><span data-stu-id="4b37c-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="4b37c-124">En el caso de compatibilidad con la pluralización, se pueden definir más entradas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="4b37c-125">`msgid_plural`: la cadena en plural sin traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="4b37c-126">`msgstr[0]`: la cadena traducida para el caso 0.</span><span class="sxs-lookup"><span data-stu-id="4b37c-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="4b37c-127">`msgstr[N]`: la cadena traducida para el caso N.</span><span class="sxs-lookup"><span data-stu-id="4b37c-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="4b37c-128">Encontrará [aquí](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html) la especificación del archivo de objeto portátil.</span><span class="sxs-lookup"><span data-stu-id="4b37c-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="4b37c-129">Configuración de la compatibilidad con archivos de objeto portátil en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b37c-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="4b37c-130">Este ejemplo se basa en una aplicación ASP.NET Core MVC generada a partir de una plantilla de proyecto de Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="4b37c-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="4b37c-131">Hacer referencia al paquete</span><span class="sxs-lookup"><span data-stu-id="4b37c-131">Referencing the package</span></span>

<span data-ttu-id="4b37c-132">Agregue una referencia al paquete NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="4b37c-133">El archivo *.csproj* ahora contiene una línea similar a la siguiente (el número de versión puede variar):</span><span class="sxs-lookup"><span data-stu-id="4b37c-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="4b37c-134">Registrar el servicio</span><span class="sxs-lookup"><span data-stu-id="4b37c-134">Registering the service</span></span>

<span data-ttu-id="4b37c-135">Agregue los servicios necesarios al método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b37c-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="4b37c-136">Agregue el software intermedio necesario al método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b37c-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="4b37c-137">Agregue el código siguiente a la vista de Razor que quiera.</span><span class="sxs-lookup"><span data-stu-id="4b37c-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="4b37c-138">En este ejemplo se usa *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="4b37c-139">Se inserta una instancia de `IViewLocalizer` que se usa para traducir el texto "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="4b37c-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="4b37c-140">Crear un archivo de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="4b37c-140">Creating a PO file</span></span>

<span data-ttu-id="4b37c-141">Cree un archivo denominado *\<culture code>.po* en la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b37c-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="4b37c-142">En este ejemplo, el nombre del archivo es *fr.po* porque se usa el idioma francés:</span><span class="sxs-lookup"><span data-stu-id="4b37c-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="4b37c-143">En este archivo se almacenan la cadena que se va a traducir y la cadena traducida al francés.</span><span class="sxs-lookup"><span data-stu-id="4b37c-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="4b37c-144">Si es necesario, las traducciones se revierten a su referencia cultural principal.</span><span class="sxs-lookup"><span data-stu-id="4b37c-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="4b37c-145">En este ejemplo, el archivo *fr.po* se usa si la referencia cultural solicitada es `fr-FR` o `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="4b37c-146">Probar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4b37c-146">Testing the application</span></span>

<span data-ttu-id="4b37c-147">Ejecute la aplicación y vaya a la URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="4b37c-148">El texto **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="4b37c-148">The text **Hello world!**</span></span> <span data-ttu-id="4b37c-149">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="4b37c-149">is displayed.</span></span>

<span data-ttu-id="4b37c-150">Vaya a la dirección URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="4b37c-151">El texto **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="4b37c-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="4b37c-152">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="4b37c-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="4b37c-153">Pluralización</span><span class="sxs-lookup"><span data-stu-id="4b37c-153">Pluralization</span></span>

<span data-ttu-id="4b37c-154">Los archivos de objeto portátil son compatibles con las formas de pluralización, lo que resulta útil cuando es necesario traducir la misma cadena de forma diferente en función de una cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="4b37c-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="4b37c-155">Esta tarea se ve dificultada por el hecho de que cada idioma define reglas personalizadas para seleccionar qué cadena se va a usar en función de la cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="4b37c-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="4b37c-156">El paquete de localización de Orchard proporciona una API para invocar automáticamente estas diferentes formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="4b37c-157">Crear archivos de objeto portátil de pluralización</span><span class="sxs-lookup"><span data-stu-id="4b37c-157">Creating pluralization PO files</span></span>

<span data-ttu-id="4b37c-158">Agregue el contenido siguiente al archivo *fr.po* mencionado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="4b37c-159">Vea [¿Qué es un archivo de objeto portátil?](#what-is-a-po-file) para saber qué representa cada entrada de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4b37c-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="4b37c-160">Agregar un idioma con formas de pluralización diferentes</span><span class="sxs-lookup"><span data-stu-id="4b37c-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="4b37c-161">En el ejemplo anterior se han usado cadenas en inglés y francés.</span><span class="sxs-lookup"><span data-stu-id="4b37c-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="4b37c-162">El idioma inglés y francés solo tienen dos formas de pluralización y comparten las mismas reglas de formato, es decir, se asigna una cardinalidad de uno a la primera forma plural.</span><span class="sxs-lookup"><span data-stu-id="4b37c-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="4b37c-163">Todas las demás cardinalidades se asignan a la segunda forma plural.</span><span class="sxs-lookup"><span data-stu-id="4b37c-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="4b37c-164">No todos los idiomas comparten las mismas reglas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-164">Not all languages share the same rules.</span></span> <span data-ttu-id="4b37c-165">Esto se muestra con el idioma checo, que tiene tres formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="4b37c-166">Cree el archivo `cs.po` como se indica a continuación y observe que la pluralización requiere tres traducciones diferentes:</span><span class="sxs-lookup"><span data-stu-id="4b37c-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="4b37c-167">Para aceptar localizaciones en checo, agregue `"cs"` a la lista de referencias culturales admitidas en el método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="4b37c-168">Edite el archivo *Views/Home/About.cshtml* para representar cadenas localizadas en plural para diversas cardinalidades:</span><span class="sxs-lookup"><span data-stu-id="4b37c-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="4b37c-169">**Nota:** En un escenario real, se usaría una variable para representar el número.</span><span class="sxs-lookup"><span data-stu-id="4b37c-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="4b37c-170">En este caso, repetimos el mismo código con tres valores diferentes para exponer un caso muy específico.</span><span class="sxs-lookup"><span data-stu-id="4b37c-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="4b37c-171">Si cambia las referencias culturales, verá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="4b37c-172">Para `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="4b37c-173">Para `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="4b37c-174">Para `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="4b37c-175">Tenga en cuenta que para la referencia cultural de checo, las tres traducciones son diferentes.</span><span class="sxs-lookup"><span data-stu-id="4b37c-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="4b37c-176">Las referencias culturales de inglés y francés comparten la misma construcción para las dos últimas cadenas traducidas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="4b37c-177">Tareas avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b37c-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="4b37c-178">Contextualización de cadenas</span><span class="sxs-lookup"><span data-stu-id="4b37c-178">Contextualizing strings</span></span>

<span data-ttu-id="4b37c-179">Las aplicaciones a menudo contienen las cadenas que se van a traducir en lugares diferentes.</span><span class="sxs-lookup"><span data-stu-id="4b37c-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="4b37c-180">La misma cadena puede tener una traducción diferente en determinadas ubicaciones dentro de una aplicación (vistas de Razor o archivos de clase).</span><span class="sxs-lookup"><span data-stu-id="4b37c-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="4b37c-181">Un archivo de objeto portátil admite el concepto de contexto de archivo, que se puede usar para clasificar la cadena que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="4b37c-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="4b37c-182">Mediante el uso de un contexto de archivo, una cadena se puede traducir de forma diferente según el contexto de archivo (o según la falta de contexto de archivo).</span><span class="sxs-lookup"><span data-stu-id="4b37c-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="4b37c-183">Los servicios de localización de objetos portátiles usan el nombre de la clase completa o la vista que se usa al traducir una cadena.</span><span class="sxs-lookup"><span data-stu-id="4b37c-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="4b37c-184">Esto se logra mediante el establecimiento del valor en la entrada `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="4b37c-185">Considere la posibilidad de realizar una adición mínima al ejemplo *fr.po* anterior.</span><span class="sxs-lookup"><span data-stu-id="4b37c-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="4b37c-186">Una vista de Razor ubicada en *Views/Home/About.cshtml* se puede definir como el contexto de archivo si se establece el valor de la entrada reservado `msgctxt`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="4b37c-187">Después de establecer `msgctxt`, el texto se traduce cuando se va a `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="4b37c-188">La traducción no se llevará a cabo al ir a `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="4b37c-189">Cuando ninguna entrada específica coincide con un contexto de archivo determinado, el mecanismo de reserva de Orchard Core busca un archivo de objeto portátil adecuado sin contexto.</span><span class="sxs-lookup"><span data-stu-id="4b37c-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="4b37c-190">Suponiendo que no haya ningún contexto de archivo específico definido para *Views/Home/Contact.cshtml*, al ir a `/Home/Contact?culture=fr-FR` se carga un archivo de objeto portátil como:</span><span class="sxs-lookup"><span data-stu-id="4b37c-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="4b37c-191">Cambiar la ubicación de los archivos de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="4b37c-191">Changing the location of PO files</span></span>

<span data-ttu-id="4b37c-192">La ubicación predeterminada de los archivos de objeto portátil se puede cambiar en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="4b37c-193">En este ejemplo, los archivos de objeto portátil se cargan desde la carpeta *Localization*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="4b37c-194">Implementar una lógica personalizada para buscar archivos de localización</span><span class="sxs-lookup"><span data-stu-id="4b37c-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="4b37c-195">Cuando se necesita una lógica más compleja para buscar archivos de objeto portátil, es posible implementar y registrar la interfaz `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` como un servicio.</span><span class="sxs-lookup"><span data-stu-id="4b37c-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="4b37c-196">Esto es útil cuando los archivos de objeto portátil se pueden almacenar en ubicaciones diferentes o cuando los archivos deben encontrarse en una jerarquía de carpetas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="4b37c-197">Usar un idioma pluralizado predeterminado diferente</span><span class="sxs-lookup"><span data-stu-id="4b37c-197">Using a different default pluralized language</span></span>

<span data-ttu-id="4b37c-198">El paquete incluye un método de extensión `Plural` que es específico para dos formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="4b37c-199">Para los idiomas que requieren más formas plurales, es necesario crear un método de extensión.</span><span class="sxs-lookup"><span data-stu-id="4b37c-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="4b37c-200">Con un método de extensión, no tendrá que proporcionar ningún archivo de localización para el idioma predeterminado, dado que las cadenas originales ya están disponibles directamente en el código.</span><span class="sxs-lookup"><span data-stu-id="4b37c-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="4b37c-201">Puede usar la sobrecarga `Plural(int count, string[] pluralForms, params object[] arguments)` más genérica, que acepta una matriz de cadenas de traducciones.</span><span class="sxs-lookup"><span data-stu-id="4b37c-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b37c-202">Por [Sébastien Ros](https://github.com/sebastienros) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="4b37c-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4b37c-203">En este artículo se describen los pasos para usar archivos de objeto portátil (PO) en una aplicación ASP.NET Core con el marco de trabajo de [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="4b37c-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="4b37c-204">**Nota:** Orchard Core no es un producto de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="4b37c-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="4b37c-205">Por tanto, Microsoft no ofrece soporte técnico para esta característica.</span><span class="sxs-lookup"><span data-stu-id="4b37c-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="4b37c-206">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b37c-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="4b37c-207">¿Qué es un archivo de objeto portátil?</span><span class="sxs-lookup"><span data-stu-id="4b37c-207">What is a PO file?</span></span>

<span data-ttu-id="4b37c-208">Los archivos de objeto portátil se distribuyen como archivos de texto que contienen las cadenas traducidas de un idioma determinado.</span><span class="sxs-lookup"><span data-stu-id="4b37c-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="4b37c-209">Entre las ventajas de usar archivos de objeto portátil en lugar de archivos *.resx* se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="4b37c-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="4b37c-210">Los archivos de objeto portátil admiten la pluralización, a diferencia de los archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="4b37c-211">Los archivos de objeto portátil no se compilan como archivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="4b37c-212">Por tanto, no se requieren herramientas ni pasos de compilación especializados.</span><span class="sxs-lookup"><span data-stu-id="4b37c-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="4b37c-213">Los archivos de objeto portátil funcionan bien con herramientas de colaboración de edición en línea.</span><span class="sxs-lookup"><span data-stu-id="4b37c-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="4b37c-214">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="4b37c-214">Example</span></span>

<span data-ttu-id="4b37c-215">Este es un archivo de objeto portátil de ejemplo que contiene la traducción de dos cadenas en francés, incluida una con su forma en plural:</span><span class="sxs-lookup"><span data-stu-id="4b37c-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="4b37c-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="4b37c-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="4b37c-217">En este ejemplo se usa la sintaxis siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="4b37c-218">`#:`: comentario que indica el contexto de la cadena que se va a traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="4b37c-219">La misma cadena se podría traducir de forma diferente según donde se vaya a usar.</span><span class="sxs-lookup"><span data-stu-id="4b37c-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="4b37c-220">`msgid`: la cadena sin traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="4b37c-221">`msgstr`: la cadena traducida.</span><span class="sxs-lookup"><span data-stu-id="4b37c-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="4b37c-222">En el caso de compatibilidad con la pluralización, se pueden definir más entradas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="4b37c-223">`msgid_plural`: la cadena en plural sin traducir.</span><span class="sxs-lookup"><span data-stu-id="4b37c-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="4b37c-224">`msgstr[0]`: la cadena traducida para el caso 0.</span><span class="sxs-lookup"><span data-stu-id="4b37c-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="4b37c-225">`msgstr[N]`: la cadena traducida para el caso N.</span><span class="sxs-lookup"><span data-stu-id="4b37c-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="4b37c-226">Encontrará [aquí](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html) la especificación del archivo de objeto portátil.</span><span class="sxs-lookup"><span data-stu-id="4b37c-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="4b37c-227">Configuración de la compatibilidad con archivos de objeto portátil en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b37c-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="4b37c-228">Este ejemplo se basa en una aplicación ASP.NET Core MVC generada a partir de una plantilla de proyecto de Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="4b37c-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="4b37c-229">Hacer referencia al paquete</span><span class="sxs-lookup"><span data-stu-id="4b37c-229">Referencing the package</span></span>

<span data-ttu-id="4b37c-230">Agregue una referencia al paquete NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="4b37c-231">El archivo *.csproj* ahora contiene una línea similar a la siguiente (el número de versión puede variar):</span><span class="sxs-lookup"><span data-stu-id="4b37c-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="4b37c-232">Registrar el servicio</span><span class="sxs-lookup"><span data-stu-id="4b37c-232">Registering the service</span></span>

<span data-ttu-id="4b37c-233">Agregue los servicios necesarios al método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b37c-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="4b37c-234">Agregue el software intermedio necesario al método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b37c-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="4b37c-235">Agregue el código siguiente a la vista de Razor que quiera.</span><span class="sxs-lookup"><span data-stu-id="4b37c-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="4b37c-236">En este ejemplo se usa *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="4b37c-237">Se inserta una instancia de `IViewLocalizer` que se usa para traducir el texto "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="4b37c-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="4b37c-238">Crear un archivo de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="4b37c-238">Creating a PO file</span></span>

<span data-ttu-id="4b37c-239">Cree un archivo denominado *\<culture code>.po* en la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4b37c-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="4b37c-240">En este ejemplo, el nombre del archivo es *fr.po* porque se usa el idioma francés:</span><span class="sxs-lookup"><span data-stu-id="4b37c-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="4b37c-241">En este archivo se almacenan la cadena que se va a traducir y la cadena traducida al francés.</span><span class="sxs-lookup"><span data-stu-id="4b37c-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="4b37c-242">Si es necesario, las traducciones se revierten a su referencia cultural principal.</span><span class="sxs-lookup"><span data-stu-id="4b37c-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="4b37c-243">En este ejemplo, el archivo *fr.po* se usa si la referencia cultural solicitada es `fr-FR` o `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="4b37c-244">Probar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4b37c-244">Testing the application</span></span>

<span data-ttu-id="4b37c-245">Ejecute la aplicación y vaya a la URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="4b37c-246">El texto **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="4b37c-246">The text **Hello world!**</span></span> <span data-ttu-id="4b37c-247">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="4b37c-247">is displayed.</span></span>

<span data-ttu-id="4b37c-248">Vaya a la dirección URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="4b37c-249">El texto **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="4b37c-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="4b37c-250">aparece en pantalla.</span><span class="sxs-lookup"><span data-stu-id="4b37c-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="4b37c-251">Pluralización</span><span class="sxs-lookup"><span data-stu-id="4b37c-251">Pluralization</span></span>

<span data-ttu-id="4b37c-252">Los archivos de objeto portátil son compatibles con las formas de pluralización, lo que resulta útil cuando es necesario traducir la misma cadena de forma diferente en función de una cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="4b37c-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="4b37c-253">Esta tarea se ve dificultada por el hecho de que cada idioma define reglas personalizadas para seleccionar qué cadena se va a usar en función de la cardinalidad.</span><span class="sxs-lookup"><span data-stu-id="4b37c-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="4b37c-254">El paquete de localización de Orchard proporciona una API para invocar automáticamente estas diferentes formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="4b37c-255">Crear archivos de objeto portátil de pluralización</span><span class="sxs-lookup"><span data-stu-id="4b37c-255">Creating pluralization PO files</span></span>

<span data-ttu-id="4b37c-256">Agregue el contenido siguiente al archivo *fr.po* mencionado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="4b37c-257">Vea [¿Qué es un archivo de objeto portátil?](#what-is-a-po-file) para saber qué representa cada entrada de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4b37c-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="4b37c-258">Agregar un idioma con formas de pluralización diferentes</span><span class="sxs-lookup"><span data-stu-id="4b37c-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="4b37c-259">En el ejemplo anterior se han usado cadenas en inglés y francés.</span><span class="sxs-lookup"><span data-stu-id="4b37c-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="4b37c-260">El idioma inglés y francés solo tienen dos formas de pluralización y comparten las mismas reglas de formato, es decir, se asigna una cardinalidad de uno a la primera forma plural.</span><span class="sxs-lookup"><span data-stu-id="4b37c-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="4b37c-261">Todas las demás cardinalidades se asignan a la segunda forma plural.</span><span class="sxs-lookup"><span data-stu-id="4b37c-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="4b37c-262">No todos los idiomas comparten las mismas reglas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-262">Not all languages share the same rules.</span></span> <span data-ttu-id="4b37c-263">Esto se muestra con el idioma checo, que tiene tres formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="4b37c-264">Cree el archivo `cs.po` como se indica a continuación y observe que la pluralización requiere tres traducciones diferentes:</span><span class="sxs-lookup"><span data-stu-id="4b37c-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="4b37c-265">Para aceptar localizaciones en checo, agregue `"cs"` a la lista de referencias culturales admitidas en el método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="4b37c-266">Edite el archivo *Views/Home/About.cshtml* para representar cadenas localizadas en plural para diversas cardinalidades:</span><span class="sxs-lookup"><span data-stu-id="4b37c-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="4b37c-267">**Nota:** En un escenario real, se usaría una variable para representar el número.</span><span class="sxs-lookup"><span data-stu-id="4b37c-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="4b37c-268">En este caso, repetimos el mismo código con tres valores diferentes para exponer un caso muy específico.</span><span class="sxs-lookup"><span data-stu-id="4b37c-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="4b37c-269">Si cambia las referencias culturales, verá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4b37c-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="4b37c-270">Para `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="4b37c-271">Para `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="4b37c-272">Para `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="4b37c-273">Tenga en cuenta que para la referencia cultural de checo, las tres traducciones son diferentes.</span><span class="sxs-lookup"><span data-stu-id="4b37c-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="4b37c-274">Las referencias culturales de inglés y francés comparten la misma construcción para las dos últimas cadenas traducidas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="4b37c-275">Tareas avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b37c-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="4b37c-276">Contextualización de cadenas</span><span class="sxs-lookup"><span data-stu-id="4b37c-276">Contextualizing strings</span></span>

<span data-ttu-id="4b37c-277">Las aplicaciones a menudo contienen las cadenas que se van a traducir en lugares diferentes.</span><span class="sxs-lookup"><span data-stu-id="4b37c-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="4b37c-278">La misma cadena puede tener una traducción diferente en determinadas ubicaciones dentro de una aplicación (vistas de Razor o archivos de clase).</span><span class="sxs-lookup"><span data-stu-id="4b37c-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="4b37c-279">Un archivo de objeto portátil admite el concepto de contexto de archivo, que se puede usar para clasificar la cadena que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="4b37c-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="4b37c-280">Mediante el uso de un contexto de archivo, una cadena se puede traducir de forma diferente según el contexto de archivo (o según la falta de contexto de archivo).</span><span class="sxs-lookup"><span data-stu-id="4b37c-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="4b37c-281">Los servicios de localización de objetos portátiles usan el nombre de la clase completa o la vista que se usa al traducir una cadena.</span><span class="sxs-lookup"><span data-stu-id="4b37c-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="4b37c-282">Esto se logra mediante el establecimiento del valor en la entrada `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="4b37c-283">Considere la posibilidad de realizar una adición mínima al ejemplo *fr.po* anterior.</span><span class="sxs-lookup"><span data-stu-id="4b37c-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="4b37c-284">Una vista de Razor ubicada en *Views/Home/About.cshtml* se puede definir como el contexto de archivo si se establece el valor de la entrada reservado `msgctxt`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="4b37c-285">Después de establecer `msgctxt`, el texto se traduce cuando se va a `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="4b37c-286">La traducción no se llevará a cabo al ir a `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="4b37c-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="4b37c-287">Cuando ninguna entrada específica coincide con un contexto de archivo determinado, el mecanismo de reserva de Orchard Core busca un archivo de objeto portátil adecuado sin contexto.</span><span class="sxs-lookup"><span data-stu-id="4b37c-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="4b37c-288">Suponiendo que no haya ningún contexto de archivo específico definido para *Views/Home/Contact.cshtml*, al ir a `/Home/Contact?culture=fr-FR` se carga un archivo de objeto portátil como:</span><span class="sxs-lookup"><span data-stu-id="4b37c-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="4b37c-289">Cambiar la ubicación de los archivos de objeto portátil</span><span class="sxs-lookup"><span data-stu-id="4b37c-289">Changing the location of PO files</span></span>

<span data-ttu-id="4b37c-290">La ubicación predeterminada de los archivos de objeto portátil se puede cambiar en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b37c-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="4b37c-291">En este ejemplo, los archivos de objeto portátil se cargan desde la carpeta *Localization*.</span><span class="sxs-lookup"><span data-stu-id="4b37c-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="4b37c-292">Implementar una lógica personalizada para buscar archivos de localización</span><span class="sxs-lookup"><span data-stu-id="4b37c-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="4b37c-293">Cuando se necesita una lógica más compleja para buscar archivos de objeto portátil, es posible implementar y registrar la interfaz `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` como un servicio.</span><span class="sxs-lookup"><span data-stu-id="4b37c-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="4b37c-294">Esto es útil cuando los archivos de objeto portátil se pueden almacenar en ubicaciones diferentes o cuando los archivos deben encontrarse en una jerarquía de carpetas.</span><span class="sxs-lookup"><span data-stu-id="4b37c-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="4b37c-295">Usar un idioma pluralizado predeterminado diferente</span><span class="sxs-lookup"><span data-stu-id="4b37c-295">Using a different default pluralized language</span></span>

<span data-ttu-id="4b37c-296">El paquete incluye un método de extensión `Plural` que es específico para dos formas plurales.</span><span class="sxs-lookup"><span data-stu-id="4b37c-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="4b37c-297">Para los idiomas que requieren más formas plurales, es necesario crear un método de extensión.</span><span class="sxs-lookup"><span data-stu-id="4b37c-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="4b37c-298">Con un método de extensión, no tendrá que proporcionar ningún archivo de localización para el idioma predeterminado, dado que las cadenas originales ya están disponibles directamente en el código.</span><span class="sxs-lookup"><span data-stu-id="4b37c-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="4b37c-299">Puede usar la sobrecarga `Plural(int count, string[] pluralForms, params object[] arguments)` más genérica, que acepta una matriz de cadenas de traducciones.</span><span class="sxs-lookup"><span data-stu-id="4b37c-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
