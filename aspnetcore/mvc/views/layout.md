---
title: Diseño en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo usar diseños comunes, compartir directivas y ejecutar código común antes de representar vistas en una aplicación ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
no-loc:
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
uid: mvc/views/layout
ms.openlocfilehash: 308e567e0480f83972ab7a55c7b957af83a164fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630697"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="c7901-103">Diseño en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7901-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="c7901-104">Por [Steve Smith](https://ardalis.com/) y [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="c7901-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="c7901-105">Las páginas y las vistas a menudo comparten elementos visuales y elementos mediante programación.</span><span class="sxs-lookup"><span data-stu-id="c7901-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="c7901-106">En este artículo se explica cómo:</span><span class="sxs-lookup"><span data-stu-id="c7901-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="c7901-107">Usar diseños comunes.</span><span class="sxs-lookup"><span data-stu-id="c7901-107">Use common layouts.</span></span>
* <span data-ttu-id="c7901-108">Compartir directivas.</span><span class="sxs-lookup"><span data-stu-id="c7901-108">Share directives.</span></span>
* <span data-ttu-id="c7901-109">Ejecutar código común antes de representar páginas o vistas.</span><span class="sxs-lookup"><span data-stu-id="c7901-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="c7901-110">En este documento se describen los diseños de los dos enfoques diferentes para ASP.NET Core MVC: Razor páginas y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="c7901-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="c7901-111">Para este tema, las diferencias son mínimas:</span><span class="sxs-lookup"><span data-stu-id="c7901-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="c7901-112">Razor Las páginas se encuentran en la carpeta *pages* .</span><span class="sxs-lookup"><span data-stu-id="c7901-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="c7901-113">Los controladores con vistas usan una carpeta *Vistas* para las vistas.</span><span class="sxs-lookup"><span data-stu-id="c7901-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="c7901-114">Qué es un diseño</span><span class="sxs-lookup"><span data-stu-id="c7901-114">What is a Layout</span></span>

<span data-ttu-id="c7901-115">La mayoría de las aplicaciones web tienen un diseño común que ofrece al usuario una experiencia coherente mientras navegan por sus páginas.</span><span class="sxs-lookup"><span data-stu-id="c7901-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="c7901-116">El diseño suele incluir elementos comunes en la interfaz de usuario, como el encabezado, los elementos de navegación o de menú y el pie de página de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7901-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Ejemplo de diseño de página](layout/_static/page-layout.png)

<span data-ttu-id="c7901-118">Las estructuras HTML comunes, como scripts y hojas de estilo, también se usan con frecuencia en muchas páginas dentro de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7901-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="c7901-119">Todos estos elementos compartidos se pueden definir en un archivo de *diseño* , al que se puede hacer referencia a través de cualquier vista usada dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7901-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="c7901-120">Los diseños reducen el código duplicado en las vistas.</span><span class="sxs-lookup"><span data-stu-id="c7901-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="c7901-121">Por convención, el diseño predeterminado para una aplicación ASP.NET Core se denomina *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c7901-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="c7901-122">Los archivos de diseño para los nuevos proyectos de ASP.NET Core creados con las plantillas son:</span><span class="sxs-lookup"><span data-stu-id="c7901-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="c7901-123">Razor Páginas: *pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c7901-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Carpeta Pages del Explorador de soluciones](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="c7901-125">Controlador con vistas: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c7901-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Carpeta Views del Explorador de soluciones](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="c7901-127">Este diseño define una plantilla de nivel superior para las vistas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c7901-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="c7901-128">Las aplicaciones no requieren un diseño.</span><span class="sxs-lookup"><span data-stu-id="c7901-128">Apps don't require a layout.</span></span> <span data-ttu-id="c7901-129">Las aplicaciones pueden definir más de un diseño, con otras vistas que especifiquen otros diseños.</span><span class="sxs-lookup"><span data-stu-id="c7901-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="c7901-130">Este código muestra el archivo de diseño para un proyecto creado mediante plantilla con un controlador y vistas:</span><span class="sxs-lookup"><span data-stu-id="c7901-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="c7901-131">Especificar un diseño</span><span class="sxs-lookup"><span data-stu-id="c7901-131">Specifying a Layout</span></span>

<span data-ttu-id="c7901-132">Razor las vistas tienen una `Layout` propiedad.</span><span class="sxs-lookup"><span data-stu-id="c7901-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="c7901-133">Las vistas individuales especifican un diseño al configurar esta propiedad:</span><span class="sxs-lookup"><span data-stu-id="c7901-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="c7901-134">El diseño especificado puede usar una ruta de acceso completa (por ejemplo, */Pages/Shared/_Layout.cshtml* o */Views/Shared/_Layout.cshtml*) o un nombre parcial (ejemplo: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="c7901-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="c7901-135">Cuando se proporciona un nombre parcial, el Razor motor de vistas busca el archivo de diseño mediante su proceso de detección estándar.</span><span class="sxs-lookup"><span data-stu-id="c7901-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="c7901-136">Primero se busca la carpeta donde existe el método de controlador (o controlador), seguida de la carpeta *Shared*.</span><span class="sxs-lookup"><span data-stu-id="c7901-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="c7901-137">Este proceso de detección es idéntico al que se usa para detectar [vistas parciales](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="c7901-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="c7901-138">De forma predeterminada, todos los diseños deben llamar a `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="c7901-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="c7901-139">Cada vez que se realiza la llamada a `RenderBody`, se representa el contenido de la vista.</span><span class="sxs-lookup"><span data-stu-id="c7901-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="c7901-140">Secciones</span><span class="sxs-lookup"><span data-stu-id="c7901-140">Sections</span></span>

<span data-ttu-id="c7901-141">Opcionalmente, un diseño puede hacer referencia a una o varias *secciones* mediante una llamada a `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="c7901-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="c7901-142">Las secciones permiten organizar dónde se deben colocar determinados elementos de la página.</span><span class="sxs-lookup"><span data-stu-id="c7901-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="c7901-143">Cada llamada a `RenderSection` puede especificar si esa sección es obligatoria u opcional:</span><span class="sxs-lookup"><span data-stu-id="c7901-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="c7901-144">Si no se encuentra una sección obligatoria, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="c7901-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="c7901-145">Las vistas individuales especifican el contenido que se va a representar dentro de una sección mediante la `@section` Razor Sintaxis.</span><span class="sxs-lookup"><span data-stu-id="c7901-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="c7901-146">Si una página o una vista define una sección, se debe representar (o se producirá un error).</span><span class="sxs-lookup"><span data-stu-id="c7901-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="c7901-147">Una `@section` definición de ejemplo en la Razor vista páginas:</span><span class="sxs-lookup"><span data-stu-id="c7901-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="c7901-148">En el código anterior, *scripts/main.js* se agrega a la sección `scripts` en una página o vista.</span><span class="sxs-lookup"><span data-stu-id="c7901-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="c7901-149">Es posible que otras páginas o vistas de la misma aplicación no necesiten este script y no definan una sección de script.</span><span class="sxs-lookup"><span data-stu-id="c7901-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="c7901-150">El marcado siguiente usa el [asistente de etiquetas parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) para representar *_ValidationScriptsPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c7901-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="c7901-151">El marcado anterior se generó mediante [scaffolding Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="c7901-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="c7901-152">Las secciones definidas en una vista o una vista solo están disponibles en su página de diseño inmediato.</span><span class="sxs-lookup"><span data-stu-id="c7901-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="c7901-153">No se puede hacer referencia a ellas desde líneas de código parcialmente ejecutadas, componentes de vista u otros elementos del sistema de vistas.</span><span class="sxs-lookup"><span data-stu-id="c7901-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="c7901-154">Omitir secciones</span><span class="sxs-lookup"><span data-stu-id="c7901-154">Ignoring sections</span></span>

<span data-ttu-id="c7901-155">De forma predeterminada, el cuerpo y todas las secciones de una página de contenido deben representarse mediante la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="c7901-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="c7901-156">El Razor motor de vistas exige esto mediante el seguimiento de si se han representado el cuerpo y cada sección.</span><span class="sxs-lookup"><span data-stu-id="c7901-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="c7901-157">Para indicar al motor de vistas que pase por alto el cuerpo o las secciones, llame a los métodos `IgnoreBody` y `IgnoreSection`.</span><span class="sxs-lookup"><span data-stu-id="c7901-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="c7901-158">El cuerpo y todas las secciones de una Razor Página deben representarse o pasarse por alto.</span><span class="sxs-lookup"><span data-stu-id="c7901-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="c7901-159">Importar directivas compartidas</span><span class="sxs-lookup"><span data-stu-id="c7901-159">Importing Shared Directives</span></span>

<span data-ttu-id="c7901-160">Las vistas y las páginas pueden utilizar Razor directivas para importar espacios de nombres y usar la [inserción de dependencias](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="c7901-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="c7901-161">Se pueden especificar varias directivas compartidas por muchas vistas en un archivo *_ViewImports.cshtml* común.</span><span class="sxs-lookup"><span data-stu-id="c7901-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c7901-162">El archivo `_ViewImports` es compatible con estas directivas:</span><span class="sxs-lookup"><span data-stu-id="c7901-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="c7901-163">El archivo no es compatible con otras Razor características, como las funciones y las definiciones de la sección.</span><span class="sxs-lookup"><span data-stu-id="c7901-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="c7901-164">Archivo `_ViewImports.cshtml` de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c7901-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="c7901-165">El archivo *_ViewImports.cshtml* para una aplicación ASP.NET Core MVC normalmente se coloca en la carpeta *Pages* (o *Views*).</span><span class="sxs-lookup"><span data-stu-id="c7901-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="c7901-166">Un archivo *_ViewImports.cshtml* puede colocarse dentro de cualquier carpeta, en cuyo caso solo se aplicará a páginas o vistas dentro de esa carpeta y sus subcarpetas.</span><span class="sxs-lookup"><span data-stu-id="c7901-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="c7901-167">Los archivos `_ViewImports` se procesan a partir del nivel de raíz y, después, para cada carpeta que llevó a la ubicación de la propia página o vista.</span><span class="sxs-lookup"><span data-stu-id="c7901-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="c7901-168">La configuración `_ViewImports` especificada en el nivel de raíz se puede reemplazar en el nivel de carpeta.</span><span class="sxs-lookup"><span data-stu-id="c7901-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="c7901-169">Por ejemplo, supongamos que:</span><span class="sxs-lookup"><span data-stu-id="c7901-169">For example, suppose:</span></span>

* <span data-ttu-id="c7901-170">El archivo *_ViewImports.cshtml* del nivel de raíz incluye `@model MyModel1` y `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="c7901-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="c7901-171">Un archivo *_ViewImports.cshtml* de subcarpeta incluye `@model MyModel2` y `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="c7901-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="c7901-172">Las páginas y las vistas de la subcarpeta tendrán acceso a los asistentes de etiquetas y al modelo `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="c7901-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="c7901-173">Si hay varios *_ViewImports.cshtml* en la jerarquía de archivos, el comportamiento combinado de las directivas es:</span><span class="sxs-lookup"><span data-stu-id="c7901-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="c7901-174">`@addTagHelper`, `@removeTagHelper`: todos se ejecutan en orden</span><span class="sxs-lookup"><span data-stu-id="c7901-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="c7901-175">`@tagHelperPrefix`: el más cercano a la vista invalida los demás</span><span class="sxs-lookup"><span data-stu-id="c7901-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="c7901-176">`@model`: el más cercano a la vista invalida los demás</span><span class="sxs-lookup"><span data-stu-id="c7901-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="c7901-177">`@inherits`: el más cercano a la vista invalida los demás</span><span class="sxs-lookup"><span data-stu-id="c7901-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="c7901-178">`@using`: todos se incluyen y se omiten los duplicados</span><span class="sxs-lookup"><span data-stu-id="c7901-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="c7901-179">`@inject`: para cada propiedad, la más cercana a la vista invalida cualquier otra con el mismo nombre de propiedad</span><span class="sxs-lookup"><span data-stu-id="c7901-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="c7901-180">Ejecutar código antes de cada vista</span><span class="sxs-lookup"><span data-stu-id="c7901-180">Running Code Before Each View</span></span>

<span data-ttu-id="c7901-181">El código que debe ejecutarse antes de cada vista o página se debería colocar en el archivo *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c7901-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="c7901-182">Por convención, el archivo *_ViewStart.cshtml* se encuentra en la carpeta *Pages* (o *Views*).</span><span class="sxs-lookup"><span data-stu-id="c7901-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="c7901-183">Las instrucciones que aparecen en *_ViewStart.cshtml* se ejecutan antes de cada vista completa (no los diseños ni las vistas parciales).</span><span class="sxs-lookup"><span data-stu-id="c7901-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="c7901-184">Al igual que [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* tiene una estructura jerárquica.</span><span class="sxs-lookup"><span data-stu-id="c7901-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="c7901-185">Si se define un archivo *_ViewStart.cshtml* en la carpeta de vista o de página, se ejecutará después del que esté definido en la raíz de la carpeta *Pages* (o *Views*) (si existe).</span><span class="sxs-lookup"><span data-stu-id="c7901-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="c7901-186">Un archivo *_ViewStart.cshtml* de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c7901-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="c7901-187">El archivo anterior especifica que todas las vistas usarán el diseño *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c7901-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="c7901-188">*_ViewStart.cshtml* y *_ViewImports.cshtml* normalmente **no** se colocan en la carpeta */Pages/Shared* (o \* /Views/Shared\*).</span><span class="sxs-lookup"><span data-stu-id="c7901-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="c7901-189">Las versiones de nivel de aplicación de estos archivos deben colocarse directamente en la carpeta */Pages* (o */Views*).</span><span class="sxs-lookup"><span data-stu-id="c7901-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
