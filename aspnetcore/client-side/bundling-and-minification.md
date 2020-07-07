---
title: Unión y minimización de recursos estáticos en ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo optimizar recursos estáticos en una aplicación web de ASP.NET Core aplicando técnicas de unión y minimización.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: de7c155189008e1f78bfb1eba062fcc86f9e4839
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401914"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="0765d-103">Unión y minimización de recursos estáticos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0765d-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="0765d-104">Por [Scott Addie](https://twitter.com/Scott_Addie) y [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="0765d-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="0765d-105">En este artículo se explican las ventajas de aplicar la unión y la minimización, así como la forma en que estas características se pueden utilizar con aplicaciones web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0765d-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="0765d-106">¿Qué son la unión y la minimización?</span><span class="sxs-lookup"><span data-stu-id="0765d-106">What is bundling and minification</span></span>

<span data-ttu-id="0765d-107">La unión y la minimización son dos optimizaciones de rendimiento distintas que se pueden aplicar en una aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0765d-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="0765d-108">Usadas de forma conjunta, la unión y la minimización mejoran el rendimiento al reducir el número de solicitudes de servidor y el tamaño de los recursos estáticos solicitados.</span><span class="sxs-lookup"><span data-stu-id="0765d-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="0765d-109">La unión y la minimización mejoran principalmente el tiempo de carga de la solicitud de la primera página.</span><span class="sxs-lookup"><span data-stu-id="0765d-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="0765d-110">Una vez solicitada una página web, el explorador almacena en caché los recursos estáticos (JavaScript, CSS e imágenes).</span><span class="sxs-lookup"><span data-stu-id="0765d-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="0765d-111">Por lo tanto, la unión y la minimización no mejoran el rendimiento cuando solicitan la misma página, o páginas, en el mismo sitio que solicita los mismos recursos.</span><span class="sxs-lookup"><span data-stu-id="0765d-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="0765d-112">Si el encabezado Expires no se establece correctamente en los recursos y no se usa la unión y la minimización, la heurística de actualización del explorador marca los recursos obsoletos pasados unos días.</span><span class="sxs-lookup"><span data-stu-id="0765d-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="0765d-113">Además, el explorador requiere una solicitud de validación para cada recurso.</span><span class="sxs-lookup"><span data-stu-id="0765d-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="0765d-114">En este caso, la unión y la minimización proporcionan una mejora del rendimiento incluso después de la solicitud de la primera página.</span><span class="sxs-lookup"><span data-stu-id="0765d-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="0765d-115">Unión</span><span class="sxs-lookup"><span data-stu-id="0765d-115">Bundling</span></span>

<span data-ttu-id="0765d-116">La unión combina varios archivos en un único archivo.</span><span class="sxs-lookup"><span data-stu-id="0765d-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="0765d-117">La unión reduce el número de solicitudes de servidor necesarias para representar un recurso web, como una página web.</span><span class="sxs-lookup"><span data-stu-id="0765d-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="0765d-118">Puede crear cualquier número de conjuntos individuales específicamente para CSS, JavaScript, etc. Menos archivos significa menos solicitudes HTTP desde el explorador al servidor o desde el servicio que proporciona la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0765d-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="0765d-119">Esto tiene como resultado una mejora en el rendimiento de la carga de la primera página.</span><span class="sxs-lookup"><span data-stu-id="0765d-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="0765d-120">Minimización</span><span class="sxs-lookup"><span data-stu-id="0765d-120">Minification</span></span>

<span data-ttu-id="0765d-121">La minimización quita caracteres innecesarios del código sin modificar la funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="0765d-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="0765d-122">El resultado es una reducción significativa del tamaño de los recursos solicitados (tales como CSS, imágenes y archivos de JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0765d-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="0765d-123">Los efectos secundarios habituales de la minimización incluyen acortar nombres de variable a un carácter y quitar comentarios y espacios en blanco innecesarios.</span><span class="sxs-lookup"><span data-stu-id="0765d-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="0765d-124">Considere la siguiente función de JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0765d-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="0765d-125">La minimización reduce la función a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0765d-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="0765d-126">Además de quitar los comentarios y los espacios en blanco innecesarios, se ha cambiado el nombre de los siguientes parámetros y variables de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="0765d-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="0765d-127">Original</span><span class="sxs-lookup"><span data-stu-id="0765d-127">Original</span></span> | <span data-ttu-id="0765d-128">Se cambia el nombre</span><span class="sxs-lookup"><span data-stu-id="0765d-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="0765d-129">Impacto de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="0765d-129">Impact of bundling and minification</span></span>

<span data-ttu-id="0765d-130">En la tabla siguiente se describen las diferencias entre la carga de recursos de forma individual y el uso de la unión y la minimización:</span><span class="sxs-lookup"><span data-stu-id="0765d-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="0765d-131">Acción</span><span class="sxs-lookup"><span data-stu-id="0765d-131">Action</span></span> | <span data-ttu-id="0765d-132">Con U/M</span><span class="sxs-lookup"><span data-stu-id="0765d-132">With B/M</span></span> | <span data-ttu-id="0765d-133">Sin U/M</span><span class="sxs-lookup"><span data-stu-id="0765d-133">Without B/M</span></span> | <span data-ttu-id="0765d-134">Cambio</span><span class="sxs-lookup"><span data-stu-id="0765d-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="0765d-135">Solicitudes de archivos</span><span class="sxs-lookup"><span data-stu-id="0765d-135">File Requests</span></span>  | <span data-ttu-id="0765d-136">7</span><span class="sxs-lookup"><span data-stu-id="0765d-136">7</span></span>   | <span data-ttu-id="0765d-137">18</span><span class="sxs-lookup"><span data-stu-id="0765d-137">18</span></span>     | <span data-ttu-id="0765d-138">157 %</span><span class="sxs-lookup"><span data-stu-id="0765d-138">157%</span></span>
<span data-ttu-id="0765d-139">KB transferidos</span><span class="sxs-lookup"><span data-stu-id="0765d-139">KB Transferred</span></span> | <span data-ttu-id="0765d-140">156</span><span class="sxs-lookup"><span data-stu-id="0765d-140">156</span></span> | <span data-ttu-id="0765d-141">264,68</span><span class="sxs-lookup"><span data-stu-id="0765d-141">264.68</span></span> | <span data-ttu-id="0765d-142">70%</span><span class="sxs-lookup"><span data-stu-id="0765d-142">70%</span></span>
<span data-ttu-id="0765d-143">Tiempo de carga (ms)</span><span class="sxs-lookup"><span data-stu-id="0765d-143">Load Time (ms)</span></span> | <span data-ttu-id="0765d-144">885</span><span class="sxs-lookup"><span data-stu-id="0765d-144">885</span></span> | <span data-ttu-id="0765d-145">2360</span><span class="sxs-lookup"><span data-stu-id="0765d-145">2360</span></span>   | <span data-ttu-id="0765d-146">167 %</span><span class="sxs-lookup"><span data-stu-id="0765d-146">167%</span></span>

<span data-ttu-id="0765d-147">Los exploradores son bastante detallados con respecto a los encabezados de solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="0765d-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="0765d-148">La métrica del número total de bytes enviados ha mostrado una reducción considerable al aplicar la unión.</span><span class="sxs-lookup"><span data-stu-id="0765d-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="0765d-149">El tiempo de carga muestra una mejora considerable, si bien este ejemplo se ha ejecutado localmente.</span><span class="sxs-lookup"><span data-stu-id="0765d-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="0765d-150">Se obtienen mayores mejoras de rendimiento al usar la unión y la minimización con recursos transferidos a través de una red.</span><span class="sxs-lookup"><span data-stu-id="0765d-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="0765d-151">Elección de una estrategia de unión y minimización</span><span class="sxs-lookup"><span data-stu-id="0765d-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="0765d-152">Las plantillas de proyecto de MVC y Razor Pages proporcionan una solución para la unión y la minificación que consta de un archivo de configuración JSON.</span><span class="sxs-lookup"><span data-stu-id="0765d-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="0765d-153">Las herramientas de terceros, como el ejecutor de tareas [Grunt](xref:client-side/using-grunt), realizan las mismas tareas con un poco más de complejidad.</span><span class="sxs-lookup"><span data-stu-id="0765d-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="0765d-154">Una herramienta de terceros es una buena opción cuando el flujo de trabajo de desarrollo requiere un procesamiento más allá de la unión y la minimización, como la detección de errores y la optimización de imágenes.</span><span class="sxs-lookup"><span data-stu-id="0765d-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="0765d-155">Mediante el uso de la unión y la minimización en tiempo de diseño, los archivos minimizados se crean con anterioridad a la implementación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0765d-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="0765d-156">La unión y la minimización antes de la implementación proporcionan la ventaja de reducir la carga del servidor.</span><span class="sxs-lookup"><span data-stu-id="0765d-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="0765d-157">Sin embargo, es importante reconocer que la unión y la minimización en tiempo de diseño aumentan la complejidad de la compilación y solo funcionan con archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="0765d-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="0765d-158">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="0765d-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0765d-159">En ASP.NET Core 2.0 o versiones anteriores, las plantillas de proyecto de MVC y Razor Pages proporcionan un archivo de configuración *bundleconfig.json* que define las opciones de cada conjunto:</span><span class="sxs-lookup"><span data-stu-id="0765d-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0765d-160">En ASP.NET Core 2.1 o versiones posteriores, agregue un archivo JSON, denominado *bundleconfig.json*, a la raíz del proyecto de MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0765d-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="0765d-161">Incluya el código JSON siguiente en ese archivo como punto inicial:</span><span class="sxs-lookup"><span data-stu-id="0765d-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="0765d-162">El archivo *bundleconfig.json* define las opciones de cada conjunto.</span><span class="sxs-lookup"><span data-stu-id="0765d-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="0765d-163">En el ejemplo anterior, se define una configuración de conjunto única para los archivos personalizados de JavaScript (*wwwroot/JS/site.js*) y de hoja de estilo (*wwwroot/CSS/site.css*).</span><span class="sxs-lookup"><span data-stu-id="0765d-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="0765d-164">Las opciones de configuración incluyen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0765d-164">Configuration options include:</span></span>

* <span data-ttu-id="0765d-165">`outputFileName`: nombre del archivo de conjunto que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="0765d-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="0765d-166">Puede contener una ruta de acceso relativa del archivo *bundleconfig.json*.</span><span class="sxs-lookup"><span data-stu-id="0765d-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="0765d-167">**requerido**</span><span class="sxs-lookup"><span data-stu-id="0765d-167">**required**</span></span>
* <span data-ttu-id="0765d-168">`inputFiles`: matriz de archivos que se van a unir.</span><span class="sxs-lookup"><span data-stu-id="0765d-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="0765d-169">Se trata de rutas de acceso relativas al archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="0765d-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="0765d-170">**opcional**, \*un valor vacío da como resultado un archivo de salida vacío.</span><span class="sxs-lookup"><span data-stu-id="0765d-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="0765d-171">Se admiten los patrones [globales](https://www.tldp.org/LDP/abs/html/globbingref.html).</span><span class="sxs-lookup"><span data-stu-id="0765d-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="0765d-172">`minify`: opciones de minimización para el tipo de salida.</span><span class="sxs-lookup"><span data-stu-id="0765d-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="0765d-173">**opcional**, *valor predeterminado: `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="0765d-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="0765d-174">Las opciones de configuración están disponibles por tipo de archivo de salida.</span><span class="sxs-lookup"><span data-stu-id="0765d-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="0765d-175">Minimizador CSS</span><span class="sxs-lookup"><span data-stu-id="0765d-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="0765d-176">Minimizador de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0765d-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="0765d-177">Minimizador HTML</span><span class="sxs-lookup"><span data-stu-id="0765d-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="0765d-178">`includeInProject`: marca que indica si se van a agregar los archivos generados al archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0765d-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="0765d-179">**opcional**, *valor predeterminado: false*</span><span class="sxs-lookup"><span data-stu-id="0765d-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="0765d-180">`sourceMap`: marca que indica si se debe generar un mapa de origen para el archivo unido.</span><span class="sxs-lookup"><span data-stu-id="0765d-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="0765d-181">**opcional**, *valor predeterminado: false*</span><span class="sxs-lookup"><span data-stu-id="0765d-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="0765d-182">`sourceMapRootPath`: ruta de acceso raíz para almacenar el archivo de asignación de origen generado.</span><span class="sxs-lookup"><span data-stu-id="0765d-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="0765d-183">Incorporación de archivos al flujo de trabajo</span><span class="sxs-lookup"><span data-stu-id="0765d-183">Add files to workflow</span></span>

<span data-ttu-id="0765d-184">Considere un ejemplo en el que se agrega un archivo *custom.css* adicional similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="0765d-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="0765d-185">Para minimizar *custom.css* y unirlo con *site.css* en un archivo *site.min.css*, agregue la ruta de acceso relativa a *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="0765d-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="0765d-186">También podría usarse el patrón global siguiente:</span><span class="sxs-lookup"><span data-stu-id="0765d-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="0765d-187">Este patrón global coincide con todos los archivos CSS y excluye el patrón de archivo minimizado.</span><span class="sxs-lookup"><span data-stu-id="0765d-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="0765d-188">Compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0765d-188">Build the application.</span></span> <span data-ttu-id="0765d-189">Abra *site.min.css* y observe que el contenido de *custom.css* se anexa al final del archivo.</span><span class="sxs-lookup"><span data-stu-id="0765d-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="0765d-190">Unión y minimización basadas en entorno</span><span class="sxs-lookup"><span data-stu-id="0765d-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="0765d-191">Como procedimiento recomendado, los archivos unidos y minimizados de la aplicación deben usarse en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="0765d-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="0765d-192">Durante el desarrollo, los archivos originales facilitan la depuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0765d-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="0765d-193">Especifique los archivos que se van a incluir en las páginas mediante el [Asistente de etiquetas de entorno](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) en las vistas.</span><span class="sxs-lookup"><span data-stu-id="0765d-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="0765d-194">El Asistente de etiquetas de entorno solo representa su contenido cuando se ejecuta en [entornos](xref:fundamentals/environments) concretos.</span><span class="sxs-lookup"><span data-stu-id="0765d-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0765d-195">La etiqueta `environment` siguiente representa los archivos CSS no procesados cuando se ejecuta en el entorno `Development`:</span><span class="sxs-lookup"><span data-stu-id="0765d-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="0765d-196">La etiqueta `environment` siguiente representa los archivos CSS unidos y minimizados cuando se ejecuta en un entorno distinto de `Development`.</span><span class="sxs-lookup"><span data-stu-id="0765d-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="0765d-197">Por ejemplo, la ejecución en `Production` o `Staging` desencadena la representación de estas hojas de estilo:</span><span class="sxs-lookup"><span data-stu-id="0765d-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="0765d-198">Consumo de bundleconfig.json desde Gulp</span><span class="sxs-lookup"><span data-stu-id="0765d-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="0765d-199">Hay casos en los que el flujo de trabajo de unión y minimización de una aplicación requiere un procesamiento adicional.</span><span class="sxs-lookup"><span data-stu-id="0765d-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="0765d-200">Entre los ejemplos se incluyen la optimización de imágenes, la limpieza de memoria caché y el procesamiento de recursos de red CDN.</span><span class="sxs-lookup"><span data-stu-id="0765d-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="0765d-201">Para cumplir estos requisitos, se puede convertir el flujo de trabajo de unión y minimización para usar Gulp.</span><span class="sxs-lookup"><span data-stu-id="0765d-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="0765d-202">Conversión manual del flujo de unión y minificación para usar Gulp</span><span class="sxs-lookup"><span data-stu-id="0765d-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="0765d-203">Agregue un archivo *package.json*, con el elemento `devDependencies` siguiente, a la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="0765d-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="0765d-204">El módulo `gulp-uglify` no es compatible con ECMAScript (ES) 2015/ES6 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="0765d-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="0765d-205">Instale [gulp-terser](https://www.npmjs.com/package/gulp-terser) en lugar de `gulp-uglify` para usar ES2015/ES6 o versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="0765d-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="0765d-206">Ejecute el comando siguiente al mismo nivel que *package.json* para instalar las dependencias:</span><span class="sxs-lookup"><span data-stu-id="0765d-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="0765d-207">Instale la CLI de Gulp como una dependencia global:</span><span class="sxs-lookup"><span data-stu-id="0765d-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="0765d-208">Copie el archivo *gulpfile.js* siguiente en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="0765d-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="0765d-209">Ejecución de tareas de Gulp</span><span class="sxs-lookup"><span data-stu-id="0765d-209">Run Gulp tasks</span></span>

<span data-ttu-id="0765d-210">Para desencadenar la tarea de minimización de Gulp antes de que el proyecto se compile en Visual Studio, agregue el [destino de MSBuild](/visualstudio/msbuild/msbuild-targets) siguiente al archivo \*.csproj:</span><span class="sxs-lookup"><span data-stu-id="0765d-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="0765d-211">En este ejemplo, las tareas definidas en el destino `MyPreCompileTarget` se ejecutan antes que el destino `Build` predefinido.</span><span class="sxs-lookup"><span data-stu-id="0765d-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="0765d-212">Aparece una salida similar a la siguiente en la ventana de salida de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0765d-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="0765d-213">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0765d-213">Additional resources</span></span>

* [<span data-ttu-id="0765d-214">Uso de Grunt</span><span class="sxs-lookup"><span data-stu-id="0765d-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="0765d-215">Uso de varios entornos</span><span class="sxs-lookup"><span data-stu-id="0765d-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="0765d-216">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="0765d-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
