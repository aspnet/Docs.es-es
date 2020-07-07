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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Unión y minimización de recursos estáticos en ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) y [David Pine](https://twitter.com/davidpine7)

En este artículo se explican las ventajas de aplicar la unión y la minimización, así como la forma en que estas características se pueden utilizar con aplicaciones web de ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>¿Qué son la unión y la minimización?

La unión y la minimización son dos optimizaciones de rendimiento distintas que se pueden aplicar en una aplicación web. Usadas de forma conjunta, la unión y la minimización mejoran el rendimiento al reducir el número de solicitudes de servidor y el tamaño de los recursos estáticos solicitados.

La unión y la minimización mejoran principalmente el tiempo de carga de la solicitud de la primera página. Una vez solicitada una página web, el explorador almacena en caché los recursos estáticos (JavaScript, CSS e imágenes). Por lo tanto, la unión y la minimización no mejoran el rendimiento cuando solicitan la misma página, o páginas, en el mismo sitio que solicita los mismos recursos. Si el encabezado Expires no se establece correctamente en los recursos y no se usa la unión y la minimización, la heurística de actualización del explorador marca los recursos obsoletos pasados unos días. Además, el explorador requiere una solicitud de validación para cada recurso. En este caso, la unión y la minimización proporcionan una mejora del rendimiento incluso después de la solicitud de la primera página.

### <a name="bundling"></a>Unión

La unión combina varios archivos en un único archivo. La unión reduce el número de solicitudes de servidor necesarias para representar un recurso web, como una página web. Puede crear cualquier número de conjuntos individuales específicamente para CSS, JavaScript, etc. Menos archivos significa menos solicitudes HTTP desde el explorador al servidor o desde el servicio que proporciona la aplicación. Esto tiene como resultado una mejora en el rendimiento de la carga de la primera página.

### <a name="minification"></a>Minimización

La minimización quita caracteres innecesarios del código sin modificar la funcionalidad. El resultado es una reducción significativa del tamaño de los recursos solicitados (tales como CSS, imágenes y archivos de JavaScript). Los efectos secundarios habituales de la minimización incluyen acortar nombres de variable a un carácter y quitar comentarios y espacios en blanco innecesarios.

Considere la siguiente función de JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

La minimización reduce la función a lo siguiente:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Además de quitar los comentarios y los espacios en blanco innecesarios, se ha cambiado el nombre de los siguientes parámetros y variables de la siguiente manera:

Original | Se cambia el nombre
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impacto de la unión y la minimización

En la tabla siguiente se describen las diferencias entre la carga de recursos de forma individual y el uso de la unión y la minimización:

Acción | Con U/M | Sin U/M | Cambio
--- | :---: | :---: | :---:
Solicitudes de archivos  | 7   | 18     | 157 %
KB transferidos | 156 | 264,68 | 70%
Tiempo de carga (ms) | 885 | 2360   | 167 %

Los exploradores son bastante detallados con respecto a los encabezados de solicitud HTTP. La métrica del número total de bytes enviados ha mostrado una reducción considerable al aplicar la unión. El tiempo de carga muestra una mejora considerable, si bien este ejemplo se ha ejecutado localmente. Se obtienen mayores mejoras de rendimiento al usar la unión y la minimización con recursos transferidos a través de una red.

## <a name="choose-a-bundling-and-minification-strategy"></a>Elección de una estrategia de unión y minimización

Las plantillas de proyecto de MVC y Razor Pages proporcionan una solución para la unión y la minificación que consta de un archivo de configuración JSON. Las herramientas de terceros, como el ejecutor de tareas [Grunt](xref:client-side/using-grunt), realizan las mismas tareas con un poco más de complejidad. Una herramienta de terceros es una buena opción cuando el flujo de trabajo de desarrollo requiere un procesamiento más allá de la unión y la minimización, como la detección de errores y la optimización de imágenes. Mediante el uso de la unión y la minimización en tiempo de diseño, los archivos minimizados se crean con anterioridad a la implementación de la aplicación. La unión y la minimización antes de la implementación proporcionan la ventaja de reducir la carga del servidor. Sin embargo, es importante reconocer que la unión y la minimización en tiempo de diseño aumentan la complejidad de la compilación y solo funcionan con archivos estáticos.

## <a name="configure-bundling-and-minification"></a>Configuración de la unión y la minimización

::: moniker range="<= aspnetcore-2.0"

En ASP.NET Core 2.0 o versiones anteriores, las plantillas de proyecto de MVC y Razor Pages proporcionan un archivo de configuración *bundleconfig.json* que define las opciones de cada conjunto:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

En ASP.NET Core 2.1 o versiones posteriores, agregue un archivo JSON, denominado *bundleconfig.json*, a la raíz del proyecto de MVC o Razor Pages. Incluya el código JSON siguiente en ese archivo como punto inicial:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

El archivo *bundleconfig.json* define las opciones de cada conjunto. En el ejemplo anterior, se define una configuración de conjunto única para los archivos personalizados de JavaScript (*wwwroot/JS/site.js*) y de hoja de estilo (*wwwroot/CSS/site.css*).

Las opciones de configuración incluyen lo siguiente:

* `outputFileName`: nombre del archivo de conjunto que se va a generar. Puede contener una ruta de acceso relativa del archivo *bundleconfig.json*. **requerido**
* `inputFiles`: matriz de archivos que se van a unir. Se trata de rutas de acceso relativas al archivo de configuración. **opcional**, *un valor vacío da como resultado un archivo de salida vacío. Se admiten los patrones [globales](https://www.tldp.org/LDP/abs/html/globbingref.html).
* `minify`: opciones de minimización para el tipo de salida. **opcional**, *valor predeterminado: `minify: { enabled: true }`*
  * Las opciones de configuración están disponibles por tipo de archivo de salida.
    * [Minimizador CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minimizador de JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minimizador HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: marca que indica si se van a agregar los archivos generados al archivo del proyecto. **opcional**, *valor predeterminado: false*
* `sourceMap`: marca que indica si se debe generar un mapa de origen para el archivo unido. **opcional**, *valor predeterminado: false*
* `sourceMapRootPath`: ruta de acceso raíz para almacenar el archivo de asignación de origen generado.

## <a name="add-files-to-workflow"></a>Incorporación de archivos al flujo de trabajo

Considere un ejemplo en el que se agrega un archivo *custom.css* adicional similar al siguiente:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Para minimizar *custom.css* y unirlo con *site.css* en un archivo *site.min.css*, agregue la ruta de acceso relativa a *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> También podría usarse el patrón global siguiente:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Este patrón global coincide con todos los archivos CSS y excluye el patrón de archivo minimizado.

Compile la aplicación. Abra *site.min.css* y observe que el contenido de *custom.css* se anexa al final del archivo.

## <a name="environment-based-bundling-and-minification"></a>Unión y minimización basadas en entorno

Como procedimiento recomendado, los archivos unidos y minimizados de la aplicación deben usarse en un entorno de producción. Durante el desarrollo, los archivos originales facilitan la depuración de la aplicación.

Especifique los archivos que se van a incluir en las páginas mediante el [Asistente de etiquetas de entorno](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) en las vistas. El Asistente de etiquetas de entorno solo representa su contenido cuando se ejecuta en [entornos](xref:fundamentals/environments) concretos.

La etiqueta `environment` siguiente representa los archivos CSS no procesados cuando se ejecuta en el entorno `Development`:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

La etiqueta `environment` siguiente representa los archivos CSS unidos y minimizados cuando se ejecuta en un entorno distinto de `Development`. Por ejemplo, la ejecución en `Production` o `Staging` desencadena la representación de estas hojas de estilo:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Consumo de bundleconfig.json desde Gulp

Hay casos en los que el flujo de trabajo de unión y minimización de una aplicación requiere un procesamiento adicional. Entre los ejemplos se incluyen la optimización de imágenes, la limpieza de memoria caché y el procesamiento de recursos de red CDN. Para cumplir estos requisitos, se puede convertir el flujo de trabajo de unión y minimización para usar Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Conversión manual del flujo de unión y minificación para usar Gulp

Agregue un archivo *package.json*, con el elemento `devDependencies` siguiente, a la raíz del proyecto:

> [!WARNING]
> El módulo `gulp-uglify` no es compatible con ECMAScript (ES) 2015/ES6 y versiones posteriores. Instale [gulp-terser](https://www.npmjs.com/package/gulp-terser) en lugar de `gulp-uglify` para usar ES2015/ES6 o versiones posteriores.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Ejecute el comando siguiente al mismo nivel que *package.json* para instalar las dependencias:

```console
npm i
```

Instale la CLI de Gulp como una dependencia global:

```console
npm i -g gulp-cli
```

Copie el archivo *gulpfile.js* siguiente en la raíz del proyecto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Ejecución de tareas de Gulp

Para desencadenar la tarea de minimización de Gulp antes de que el proyecto se compile en Visual Studio, agregue el [destino de MSBuild](/visualstudio/msbuild/msbuild-targets) siguiente al archivo *.csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

En este ejemplo, las tareas definidas en el destino `MyPreCompileTarget` se ejecutan antes que el destino `Build` predefinido. Aparece una salida similar a la siguiente en la ventana de salida de Visual Studio:

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

## <a name="additional-resources"></a>Recursos adicionales

* [Uso de Grunt](xref:client-side/using-grunt)
* [Uso de varios entornos](xref:fundamentals/environments)
* [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro)
