---
title: Asistentes de etiquetas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre qué son los asistentes de etiquetas y cómo se usan en ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: d2791422e50082eab51c0735e2209e5b829d22f2
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212502"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="e0ed1-103">Asistentes de etiquetas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0ed1-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="e0ed1-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0ed1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="e0ed1-105">Qué son los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-105">What are Tag Helpers</span></span>

<span data-ttu-id="e0ed1-106">Las aplicaciones auxiliares de etiquetas permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="e0ed1-107">Por ejemplo, la aplicación auxiliar `ImageTagHelper` integrada puede anexar un número de versión al nombre de imagen.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="e0ed1-108">Cada vez que la imagen cambia, el servidor genera una nueva versión única para la imagen, lo que garantiza que los clientes puedan obtener la imagen actual (en lugar de una imagen obsoleta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="e0ed1-109">Hay muchos asistentes de etiquetas integradas para tareas comunes (como la creación de formularios, vínculos, carga de activos, etc.) y existen muchos más a disposición en repositorios públicos de GitHub y como paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="e0ed1-110">Los asistentes de etiquetas se crean en C# y tienen como destino elementos HTML en función del nombre de elemento, el nombre de atributo o la etiqueta principal.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="e0ed1-111">Por ejemplo, la aplicación auxiliar `LabelTagHelper` integrada puede tener como destino el elemento HTML `<label>` cuando se aplican atributos `LabelTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="e0ed1-112">Si está familiarizado con las [aplicaciones auxiliares HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), las aplicaciones auxiliares de etiquetas reducen las transiciones explícitas entre HTML y C# en las Razor vistas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="e0ed1-113">En muchos casos, los asistentes de HTML proporcionan un método alternativo para un asistente de etiquetas específico, pero es importante tener en cuenta que los asistentes de etiquetas no reemplazan a los asistentes de HTML y que no hay un asistente de etiquetas para cada asistente de HTML.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="e0ed1-114">En [Comparación entre los asistentes de etiquetas y los asistentes de HTML](#tag-helpers-compared-to-html-helpers) se explican las diferencias con más detalle.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="e0ed1-115">¿Qué proporcionan los asistentes de etiquetas?</span><span class="sxs-lookup"><span data-stu-id="e0ed1-115">What Tag Helpers provide</span></span>

<span data-ttu-id="e0ed1-116">**Una experiencia de desarrollo compatible con HTML** En su mayor parte, el Razor marcado con aplicaciones auxiliares de etiquetas es similar a HTML estándar.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="e0ed1-117">Los diseñadores de front-end que no son conformes con HTML/CSS/JavaScript pueden editar Razor sin tener que aprender la sintaxis de C# Razor .</span><span class="sxs-lookup"><span data-stu-id="e0ed1-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="e0ed1-118">**Un completo entorno de IntelliSense para crear HTML y Razor marcado** esto está en contraste con las aplicaciones auxiliares HTML, el enfoque anterior para la creación del marcado en el servidor en las Razor vistas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="e0ed1-119">En [Comparación entre los asistentes de etiquetas y los asistentes de HTML](#tag-helpers-compared-to-html-helpers) se explican las diferencias con más detalle.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="e0ed1-120">En [Compatibilidad de IntelliSense con asistentes de etiquetas](#intellisense-support-for-tag-helpers) se explica el entorno de IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="e0ed1-121">Incluso los desarrolladores con experiencia en Razor c# son más productivos mediante aplicaciones auxiliares de etiquetas que escribir marcado en c# Razor .</span><span class="sxs-lookup"><span data-stu-id="e0ed1-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="e0ed1-122">**Una forma de ser más productivo y generar código más sólido, confiable y fácil de mantener con información que solo está disponible en el servidor** Por ejemplo, lo habitual a la hora de actualizar las imágenes era cambiar el nombre de la imagen cuando se modificaba.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="e0ed1-123">Las imágenes debían almacenarse en caché de forma activa por motivos de rendimiento y, a menos que se cambiase el nombre de una imagen, se corría el riesgo de que los clientes obtuviesen una copia obsoleta.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="e0ed1-124">Antes, después de editar una imagen, era necesario cambiarle el nombre y actualizar todas las referencias a la imagen en la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="e0ed1-125">No solo se trata de una gran cantidad de trabajo, sino que también es propenso a errores (puede omitir una referencia, escribir accidentalmente la cadena equivocada, etc.). El integrado `ImageTagHelper` puede hacer esto automáticamente.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="e0ed1-126">`ImageTagHelper` puede anexar un número de versión al nombre de la imagen, por lo que cada vez que la imagen cambia, el servidor genera automáticamente una nueva versión única de la imagen.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="e0ed1-127">Esto garantiza que los clientes obtengan la imagen actual.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="e0ed1-128">Esta solidez y ahorro de trabajo se consiguen de forma gratuita mediante el uso de `ImageTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="e0ed1-129">La mayoría de los asistentes de etiquetas integradas tienen como destino elementos HTML estándar y proporcionan atributos del lado servidor del elemento.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="e0ed1-130">Por ejemplo, el elemento `<input>` que muchas vistas usan en la carpeta *Views/Account* contiene el atributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="e0ed1-131">Este atributo extrae el nombre de la propiedad de modelo especificada en el HTML representado.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="e0ed1-132">Considere una Razor vista con el siguiente modelo:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-132">Consider a Razor view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="e0ed1-133">El Razor marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="e0ed1-134">Se genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="e0ed1-135">El atributo `asp-for` está disponible por medio de la propiedad `For` en [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="e0ed1-136">Vea [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring) para más información.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="e0ed1-137">Administración del ámbito de los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="e0ed1-138">El ámbito de los asistentes de etiquetas se controla mediante una combinación de `@addTagHelper`, `@removeTagHelper` y el carácter de exclusión "!".</span><span class="sxs-lookup"><span data-stu-id="e0ed1-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="e0ed1-139">`@addTagHelper` hace que los asistentes de etiquetas estén disponibles</span><span class="sxs-lookup"><span data-stu-id="e0ed1-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="e0ed1-140">Si crea una aplicación web de ASP.NET Core denominada *AuthoringTagHelpers*, el siguiente archivo *Views/_ViewImports.cshtml* se agregará al proyecto:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="e0ed1-141">La directiva `@addTagHelper` hace que los asistentes de etiquetas estén disponibles en la vista.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="e0ed1-142">En este caso, el archivo de vista es *Pages/_ViewImports.cshtml*, que heredan de forma predeterminada todos los archivos contenidos en la carpeta *Pages* y sus subcarpetas, lo que hace que los asistentes de etiquetas estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="e0ed1-143">El código anterior usa la sintaxis de comodines ("\*") para especificar que todas los asistentes de etiquetas del ensamblado especificado (*Microsoft.AspNetCore.Mvc.TagHelpers*) estarán disponibles para todos los archivos de vista del directorio o subdirectorio *Views*.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="e0ed1-144">El primer parámetro después de `@addTagHelper` especifica los asistentes de etiquetas que se van a cargar (usamos "\*" para todas los asistentes de etiquetas), y el segundo parámetro ("Microsoft.AspNetCore.Mvc.TagHelpers") especifica el ensamblado que contiene los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="e0ed1-145">*Microsoft.AspNetCore.Mvc.TagHelpers* es el ensamblado para los asistentes de etiquetas integradas de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="e0ed1-146">Para exponer todas los asistentes de etiquetas de este proyecto (que crea un ensamblado denominado *AuthoringTagHelpers*), use lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="e0ed1-147">Si el proyecto contiene un asistente `EmailTagHelper` con el espacio de nombres predeterminado (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), puede proporcionar el nombre completo (FQN) del asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="e0ed1-148">Para agregar un asistente de etiquetas a una vista con un FQN, agregue primero el FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) y, después, el nombre del ensamblado (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="e0ed1-149">La mayoría de los desarrolladores prefiere usar la sintaxis de comodines "\*".</span><span class="sxs-lookup"><span data-stu-id="e0ed1-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="e0ed1-150">La sintaxis de comodines permite insertar el carácter comodín "\*" como sufijo en un FQN.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="e0ed1-151">Por ejemplo, cualquiera de las siguientes directivas incorporará `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="e0ed1-152">Como se mencionó anteriormente, la adición de la `@addTagHelper` Directiva al archivo *views/_ViewImports. cshtml* hace que la aplicación auxiliar de etiquetas esté disponible para todos los archivos de vista del directorio *views* y los subdirectorios.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="e0ed1-153">Puede usar la directiva `@addTagHelper` en archivos de vista específicos si quiere exponer el asistente de etiquetas solo a esas vistas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="e0ed1-154">`@removeTagHelper` quita los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="e0ed1-155">`@removeTagHelper` tiene los mismos parámetros que `@addTagHelper`, y quita un asistente de etiquetas que se ha agregado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="e0ed1-156">Por ejemplo, si se aplica `@removeTagHelper` a una vista específica, se quita de la vista el asistente de etiquetas especificada.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="e0ed1-157">Si se usa `@removeTagHelper` en un archivo *Views/Folder/_ViewImports.cshtml*, se quita el asistente de etiquetas especificada de todas las vistas de *Folder*.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="e0ed1-158">Controlar el ámbito del asistente de etiquetas con el archivo *_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e0ed1-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="e0ed1-159">Si agrega un archivo *_ViewImports.cshtml* a cualquier carpeta de vistas, el motor de vistas aplica las directivas de ese archivo y del archivo *Views/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e0ed1-160">Si agregara un archivo *Views/Home/_ViewImports.cshtml* vacío para las vistas de *Home*, no se produciría ningún cambio porque el archivo *_ViewImports.cshtml* se suma.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="e0ed1-161">Todas las directivas `@addTagHelper` que agregue al archivo *Views/Home/_ViewImports.cshtml* (que no estén en el archivo predeterminado *Views/_ViewImports.cshtml*) expondrán esos asistentes de etiquetas únicamente a las vistas de la carpeta *Home*.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="e0ed1-162">Excluir elementos individuales</span><span class="sxs-lookup"><span data-stu-id="e0ed1-162">Opting out of individual elements</span></span>

<span data-ttu-id="e0ed1-163">Puede deshabilitar un asistente de etiquetas en el nivel de elemento con el carácter de exclusión ("!") del asistente de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="e0ed1-164">Por ejemplo, la validación de `Email` se deshabilita en `<span>` con el carácter de exclusión del asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="e0ed1-165">Debe aplicar el carácter de exclusión del asistente de etiquetas en la etiqueta de apertura y de cierre.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="e0ed1-166">(El editor de Visual Studio agrega automáticamente el carácter de exclusión a la etiqueta de cierre cuando se agrega uno a la etiqueta de apertura).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="e0ed1-167">Después de agregar el carácter de exclusión, el elemento y los atributos del asistente de etiquetas ya no se muestran en una fuente distinta.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="e0ed1-168">Usar `@tagHelperPrefix` para hacer explícito el uso del asistente de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="e0ed1-169">La directiva `@tagHelperPrefix` permite especificar una cadena de prefijo de etiqueta para habilitar la compatibilidad con el asistente de etiquetas y hacer explícito su uso.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="e0ed1-170">Por ejemplo, podría agregar el marcado siguiente al archivo *Views/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="e0ed1-171">En la imagen de código siguiente, el prefijo del asistente de etiquetas se establece en `th:`, por lo que solo los elementos con el prefijo `th:` admiten asistentes de etiquetas (los elementos habilitados para asistentes de etiquetas tienen una fuente distinta).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="e0ed1-172">Los elementos `<label>` y `<input>` tienen el prefijo de los asistentes de etiquetas y están habilitados para estas, a diferencia del elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="e0ed1-174">Las mismas reglas de jerarquía que se aplican a `@addTagHelper` también se aplican a `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="e0ed1-175">Asistentes de etiquetas de autocierre</span><span class="sxs-lookup"><span data-stu-id="e0ed1-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="e0ed1-176">Muchos asistentes de etiquetas no se pueden usar como etiquetas de autocierre.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="e0ed1-177">Algunos asistentes de etiquetas están diseñados para ser etiquetas de cierre.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="e0ed1-178">Si se usa un asistente de etiquetas que no se ha diseñado para ser de autocierre se suprime la salida representada.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="e0ed1-179">Si se usa el autocierre para un asistente de etiquetas, se genera una etiqueta de autocierre en la salida representada.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="e0ed1-180">Vea [esta nota](xref:mvc/views/tag-helpers/authoring#self-closing) en [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring) para más información.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="e0ed1-181">C# en el atributo/declaración de los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="e0ed1-182">Los asistentes de etiquetas no admiten C# en el área de declaraciones de etiquetas o atributos del elemento.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="e0ed1-183">Por ejemplo, el código siguiente no es válido:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="e0ed1-184">El código anterior se puede escribir como:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="e0ed1-185">Compatibilidad de IntelliSense con asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="e0ed1-186">Al crear una nueva ASP.NET Core aplicación web en Visual Studio, se agrega el paquete NuGet "Microsoft. AspNetCore. Razor . Herramientas ".</span><span class="sxs-lookup"><span data-stu-id="e0ed1-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="e0ed1-187">Este es el paquete que agrega las herramientas de los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="e0ed1-188">Considere la posibilidad de escribir un elemento HTML `<label>`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="e0ed1-189">En cuanto escriba `<l` en el editor de Visual Studio, IntelliSense mostrará elementos coincidentes:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="e0ed1-191">No solo obtendrá ayuda HTML, sino el icono (el "@" symbol with "<>" situado debajo).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="e0ed1-193">Esto identifica el elemento como el destino de los asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="e0ed1-194">Los elementos HTML puros (como `fieldset`) muestran el icono "<>".</span><span class="sxs-lookup"><span data-stu-id="e0ed1-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="e0ed1-195">Una etiqueta HTML pura `<label>` muestra la etiqueta HTML (con el tema de color de Visual Studio predeterminado) en una fuente marrón, con los atributos en rojo y los valores de atributo en azul.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="e0ed1-197">Después de escribir `<label`, IntelliSense muestra los atributos HTML/CSS disponibles y los atributos destinados al asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="e0ed1-199">La finalización de instrucciones de IntelliSense permite presionar la tecla TAB para completar la instrucción con el valor seleccionado:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="e0ed1-201">En cuanto se especifica un atributo del asistente de etiquetas, las fuentes de las etiquetas y los atributos cambian.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="e0ed1-202">Si usa el tema de color predeterminado "Azul" o "Claro" de Visual Studio, la fuente es púrpura en negrita.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="e0ed1-203">Si usa el tema "Oscuro", la fuente es verde azulado en negrita.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="e0ed1-204">Las imágenes de este documento se han realizado con el tema predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="e0ed1-206">Puede usar el acceso directo *CompleteWord* de Visual Studio (el valor [predeterminado](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) es Ctrl+barra espaciadora) entre comillas dobles (""). Ahora se encuentra en C#, como si estuviera en una clase de C#.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="e0ed1-207">IntelliSense muestra todos los métodos y propiedades en el modelo de páginas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="e0ed1-208">Los métodos y las propiedades están disponibles porque el tipo de propiedad es `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="e0ed1-209">En la imagen siguiente se edita la vista `Register`, por lo que `RegisterViewModel` está disponible.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="e0ed1-211">IntelliSense muestra las propiedades y los métodos disponibles para el modelo en la página.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="e0ed1-212">El entorno enriquecido de IntelliSense le ayuda a seleccionar la clase CSS:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![imagen](intro/_static/iclass.png)

![imagen](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="e0ed1-215">Comparación entre los asistentes de etiquetas y los asistentes de HTML</span><span class="sxs-lookup"><span data-stu-id="e0ed1-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="e0ed1-216">Las aplicaciones auxiliares de etiquetas se asocian a elementos HTML en las Razor vistas, mientras que las [aplicaciones auxiliares html](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) se invocan como métodos intercalados con HTML en las Razor vistas.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="e0ed1-217">Considere el siguiente Razor marcado, que crea una etiqueta HTML con la clase CSS "Caption":</span><span class="sxs-lookup"><span data-stu-id="e0ed1-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="e0ed1-218">El símbolo de arroba ( `@` ) indica Razor que se trata del inicio del código.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="e0ed1-219">Los dos parámetros siguientes ("FirstName" y "First Name:") son cadenas, por lo que [IntelliSense](/visualstudio/ide/using-intellisense) no sirve de ayuda.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="e0ed1-220">El último argumento:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="e0ed1-221">Es un objeto anónimo que se usa para representar atributos.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="e0ed1-222">Dado que `class` es una palabra clave reservada en C#, use el símbolo `@` para forzar a C# a interpretar `@class=` como un símbolo (nombre de propiedad).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="e0ed1-223">En el diseñador de front-end (alguien familiarizado con HTML/CSS/JavaScript y otras tecnologías de cliente, pero no está familiarizado con C# y Razor ), la mayor parte de la línea es externa.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="e0ed1-224">Es necesario crear toda la línea sin ayuda de IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="e0ed1-225">Si usa `LabelTagHelper`, se puede escribir el mismo marcado de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="e0ed1-226">Con la versión del asistente de etiquetas, en cuanto escriba `<l` en el editor de Visual Studio, IntelliSense mostrará elementos coincidentes:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="e0ed1-228">IntelliSense le ayuda a escribir toda la línea.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="e0ed1-229">En la imagen de código siguiente se muestra la parte del formulario de la vista *views/Account/Register. cshtml* Razor generada a partir de la plantilla MVC de ASP.net 4.5. x incluida con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="e0ed1-231">En el editor de Visual Studio se muestra el código de C# con un fondo gris.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="e0ed1-232">Por ejemplo, el asistente de HTML `AntiForgeryToken`:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="e0ed1-233">Se muestra con un fondo gris.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-233">is displayed with a grey background.</span></span> <span data-ttu-id="e0ed1-234">La mayor parte del marcado en la vista de registro es de C#.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="e0ed1-235">Compárelo con el método equivalente con asistentes de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="e0ed1-237">El marcado es mucho más ordenado y más fácil de leer, modificar y mantener que en el método de asistentes de HTML.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="e0ed1-238">El código de C# se reduce a la cantidad mínima que el servidor necesita conocer.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="e0ed1-239">En el editor de Visual Studio se muestra el marcado de destino de un asistente de etiquetas en una fuente distinta.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="e0ed1-240">Observe el grupo *Email*:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="e0ed1-241">Cada uno de los atributos "asp-" tiene un valor de "Email", pero "Email" no es una cadena.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="e0ed1-242">En este contexto, "Email" es la propiedad de expresión del modelo de C# para `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="e0ed1-243">El editor de Visual Studio le ayuda a escribir **todo** el marcado en el método del asistente de etiquetas del formulario de registro, mientras que Visual Studio no proporciona ninguna ayuda para la mayor parte del código en el método de asistentes de HTML.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="e0ed1-244">En [Compatibilidad de IntelliSense con asistentes de etiquetas](#intellisense-support-for-tag-helpers) se explica en detalle cómo trabajar con asistentes de etiquetas en el editor de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="e0ed1-245">Comparación entre los asistentes de etiquetas y los controles de servidor web</span><span class="sxs-lookup"><span data-stu-id="e0ed1-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="e0ed1-246">Los asistentes de etiquetas no poseen el elemento al que están asociadas; simplemente participan en la representación del elemento y el contenido.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="e0ed1-247">Los [controles de servidor web](https://msdn.microsoft.com/library/7698y1f0.aspx) de ASP.NET se declaran y se invocan en una página.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="e0ed1-248">Los [controles de servidor web](https://msdn.microsoft.com/library/zsyt68f1.aspx) tienen un ciclo de vida no trivial que puede dificultar el desarrollo y la depuración.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="e0ed1-249">Los controles de servidor web permiten agregar funcionalidad a los elementos Document Object Model (DOM) de cliente mediante el uso de un control cliente.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="e0ed1-250">Los asistentes de etiquetas no tienen ningún DOM.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="e0ed1-251">Los controles de servidor web incluyen la detección automática del explorador.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="e0ed1-252">Los asistentes de etiquetas no tienen conocimiento del explorador.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="e0ed1-253">Varios asistentes de etiquetas pueden actuar en el mismo elemento (vea [Evitar conflictos de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)), mientras que normalmente no se pueden crear controles de servidor web.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="e0ed1-254">Los asistentes de etiquetas pueden modificar la etiqueta y el contenido de los elementos HTML que tienen como ámbito, pero no modifican directamente ningún otro elemento de una página.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="e0ed1-255">Los controles de servidor web tienen un ámbito menos específico y pueden realizar acciones que afectan a otras partes de la página, lo que puede tener efectos secundarios imprevistos.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="e0ed1-256">Los controles de servidor web usan convertidores de tipos para convertir cadenas en objetos.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="e0ed1-257">Con los asistentes de etiquetas, trabajará de forma nativa en C#, por lo que no necesitará ninguna conversión de tipos.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="e0ed1-258">Los controles de servidor web usan [System.ComponentModel](/dotnet/api/system.componentmodel) para implementar el comportamiento de componentes y controles en tiempo de diseño y en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="e0ed1-259">`System.ComponentModel` incluye las clases base y las interfaces para implementar atributos y convertidores de tipos, enlazarlos con orígenes de datos y generar licencias para los componentes.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="e0ed1-260">Compare esto con los asistentes de etiquetas, que normalmente se derivan de `TagHelper`, y la clase base `TagHelper` solo expone dos métodos, `Process` y `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="e0ed1-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="e0ed1-261">Personalizar la fuente de elemento de asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="e0ed1-262">Puede personalizar la fuente y la coloración desde **herramientas**  >  **Opciones**  >  **entorno**  >  **fuentes y colores**:</span><span class="sxs-lookup"><span data-stu-id="e0ed1-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="e0ed1-264">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e0ed1-264">Additional resources</span></span>

* [<span data-ttu-id="e0ed1-265">Creación de asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="e0ed1-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="e0ed1-266">Trabajar con formularios</span><span class="sxs-lookup"><span data-stu-id="e0ed1-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="e0ed1-267">[TagHelperSamples en GitHub](https://github.com/dpaquette/TagHelperSamples) contiene ejemplos de asistentes de etiquetas para trabajar con [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e0ed1-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
