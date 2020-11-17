---
title: Parte 3, aplicación de scaffolding a Razor Pages
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360897"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Parte 3. Razor Pages con scaffolding en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).

::: moniker range=">= aspnetcore-5.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a>Páginas Create, Delete, de detalles y de edición

Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Las instancias de Razor Pages derivan de `PageModel`. Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`. El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar `RazorPagesMovieContext` a la página:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.

Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages. En la instancia de Razor Pages, se llama a `OnGetAsync` o `OnGet` para inicializar el estado de la página. En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.

Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ninguna instrucción de devolución. Por ejemplo, la página de privacidad:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return. Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor. Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.

### <a name="the-page-directive"></a>La directiva @page

La directiva `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede administrar las solicitudes. `@page` debe ser la primera directiva de Razor de una página. `@page` y `@model` son ejemplos de la transición a un marcado específico de Razor. Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.

<a name="md"></a>

### <a name="the-model-directive"></a>La directiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages. En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages. El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.


Examine la expresión lambda usada en el siguiente asistente de HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar. La expresión lambda se inspecciona, no se evalúa. Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos. Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.

### <a name="the-layout-page"></a>Página de diseño

Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad). Cada página muestra el mismo diseño de menú. El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.

Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.

Las plantillas de [diseño](xref:mvc/views/layout) permiten que el diseño del contenedor HTML:

* Esté especificado en un solo lugr.
* Se aplique en varias páginas del sitio.

Busque la línea `@RenderBody()`. `RenderBody` es un marcador de posición donde se muestran todas las vistas específicas de página, *encapsuladas* en la página de diseño. Por ejemplo, seleccione el vínculo **Privacidad** y la vista *Pages/Privacy.cshtml* se representa dentro del método `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>Propiedades ViewData y Layout

Tenga en cuenta el siguiente marcado del archivo *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

El marcado resaltado anterior es un ejemplo de Razor con una transición a C#. Los caracteres `{` y `}` delimitan un bloque de código de C#.

La clase base `PageModel` contiene una propiedad de diccionario `ViewData` que se puede usar para pasar datos a una vista. Los objetos se agregan al diccionario `ViewData` con un patrón ***clave-valor** _. En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.

La propiedad `Title` se usa en el archivo _Pages/Shared/_Layout.cshtml*. En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

La línea `@*Markup removed for brevity.*@` es un comentario de Razor. A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente. Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.

### <a name="update-the-layout"></a>Actualizar el diseño

1. Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. Reemplace el elemento anterior por el marcado siguiente:

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro). En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`. El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo. Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.

1. Guarde los cambios y pruebe la aplicación seleccionando el vínculo **RpMovie** (Película de RP). Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) en GitHub.

1. Pruebe los vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** . Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.

> [!NOTE]
> Es posible que no pueda escribir comas decimales en el campo `Price`. Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación. Consulte este [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para instrucciones sobre cómo agregar la coma decimal.

La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*. Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.

### <a name="the-no-loccreate-page-model"></a>Modelo de página Create

Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

El método `OnGet` inicializa cualquier estado necesario para la página. La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create. Más adelante en el tutorial se muestra un ejemplo del estado de inicialización de `OnGet`. El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.

La propiedad `Movie` usa el atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el [enlace de modelos](xref:mvc/models/model-binding). Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.

El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados. La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario. Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha. Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.

Si no hay ningún error de modelo:

* Los datos se guardan.
* El explorador se redirige a la página Index.

### <a name="the-no-loccreate-no-locrazor-page"></a>La página Create de una instancia de Razor Pages

Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio muestra las etiquetas siguientes con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vista de VS17 de la página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Los asistentes de etiquetas siguientes se muestran en el marcado anterior:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper). El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).

El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación. La validación se trata con más detalle en un punto posterior de esta serie.

El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.

El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.

Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este tutorial se examinan las instancias de Razor Pages creadas con la técnica scaffolding en el [tutorial anterior](xref:tutorials/razor-pages/model).

[Vea o descargue](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) un ejemplo.

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a>Páginas Create, Delete, de detalles y de edición

Examine el modelo de página *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Las instancias de Razor Pages derivan de `PageModel`. Por convención, la clase derivada de `PageModel` se denomina `<PageName>Model`. El constructor aplica la [inserción de dependencias](xref:fundamentals/dependency-injection) para agregar el `RazorPagesMovieContext` a la página. Las páginas con scaffolding siguen este patrón. Vea [Código asincrónico](xref:data/ef-rp/intro#asynchronous-code) para obtener más información sobre programación asincrónica con Entity Framework.

Cuando se efectúa una solicitud de la página, el método `OnGetAsync` devuelve una lista de películas a la instancia de Razor Pages. `OnGetAsync` o `OnGet` se invocan en una instancia de Razor Pages para inicializar el estado de esa instancia. En este caso, `OnGetAsync` obtiene una lista de películas y las muestra.

Cuando `OnGet` devuelve `void` o `OnGetAsync` devuelve `Task`, no se utiliza ningún método de devolución. Cuando el tipo de valor devuelto es `IActionResult` o `Task<IActionResult>`, se debe proporcionar una instrucción return. Por ejemplo, el método `OnPostAsync` *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Examine la instancia de Razor Pages *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor puede realizar la transición de HTML a C# o a un marcado específico de Razor. Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](xref:mvc/views/razor#razor-reserved-keywords), se realiza una transición a un marcado específico de Razor; en caso contrario, la transición se realiza a C#.

La directiva de `@page` de Razor convierte el archivo en una acción de MVC, lo que significa que puede controlar las solicitudes. `@page` debe ser la primera directiva de Razor de una página. `@page` es un ejemplo de la transición a un marcado específico de Razor. Vea [Sintaxis de Razor](xref:mvc/views/razor#razor-syntax) para obtener más información.

<a name="md"></a>

### <a name="the-model-directive"></a>La directiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La directiva `@model` especifica el tipo del modelo que se pasa a una instancia de Razor Pages. En el ejemplo anterior, la línea `@model` permite que la clase derivada de `PageModel` esté disponible en la instancia de Razor Pages. El modelo se usa en los [asistentes de HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` y `@Html.DisplayFor` de la página.

### <a name="html-helpers"></a>Asistentes de HTML

Examine la expresión lambda usada en el siguiente asistente de HTML:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

El asistente de HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspecciona la propiedad `Title` a la que se hace referencia en la expresión lambda para determinar el nombre para mostrar. La expresión lambda se inspecciona, no se evalúa. Esto significa que no hay ninguna infracción de acceso si `model`, `model.Movie` o `model.Movie[0]` son `null` o están vacíos. Al evaluar la expresión lambda (por ejemplo, con `@Html.DisplayFor(modelItem => item.Title)`), se evalúan los valores de propiedad del modelo.
### <a name="the-layout-page"></a>Página de diseño

Seleccione los vínculos de menú **RazorPagesMovie** (Película de Razor Pages), **Home** (Inicio) y **Privacy** (Privacidad). Cada página muestra el mismo diseño de menú. El diseño de menú se implementa en el archivo *Pages/Shared/_Layout.cshtml*.

Abra y examine el archivo *Pages/Shared/_Layout.cshtml*.

Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio. Busque la línea `@RenderBody()`. `RenderBody` es un marcador de posición donde se mostrarán todas las vistas específicas de página que cree, *encapsuladas* en la página de diseño. Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Pages/Privacy.cshtml** se representará dentro del método `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>Propiedades ViewData y Layout

Tenga en cuenta el siguiente código del archivo *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

El código resaltado anterior es un ejemplo de Razor con una transición a C#. Los caracteres `{` y `}` delimitan un bloque de código de C#.

La clase base `PageModel` tiene una propiedad de diccionario `ViewData` que se puede usar para agregar datos que quiera pasar a una vista. Puede agregar objetos al diccionario `ViewData` con un patrón de clave/valor. En el ejemplo anterior, la propiedad `Title` se agrega al diccionario `ViewData`.

La propiedad `Title` se usa en el archivo *Pages/Shared/_Layout.cshtml*. En el siguiente marcado se muestran las primeras líneas del archivo *_Layout.cshtml*.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

La línea `@*Markup removed for brevity.*@` es un comentario de Razor. A diferencia de los comentarios HTML `<!-- -->`, los comentarios de Razor no se envían al cliente. Consulte [Documentación web de MDN: introducción a HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obtener más información.

### <a name="update-the-layout"></a>Actualizar el diseño

Cambie el elemento `<title>` del archivo *Pages/Shared/_Layout.cshtml* para mostrar **Movie** en lugar de **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Busque el siguiente elemento delimitador en el archivo *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Reemplace el elemento anterior por el marcado siguiente.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

El elemento delimitador anterior es un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro). En este caso, se trata de el [asistente de etiquetas Anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). El atributo y valor del asistente de etiquetas `asp-page="/Movies/Index"` crea un vínculo a la instancia de Razor Pages `/Movies/Index`. El valor de atributo `asp-area` está vacío, por lo que no se usa el área del vínculo. Consulte [Áreas](xref:mvc/controllers/areas) para obtener más información.

Guarde los cambios y pruebe la aplicación haciendo clic en el vínculo **RpMovie**. Si tiene cualquier problema, consulte el archivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) en GitHub.

Pruebe los otros vínculos **Home** (Inicio), **RpMovie** (Película de RP), **Create** , **Edit** (Editar) y **Delete** . Cada página establece el título, que puede ver en la pestaña del explorador. Al marcar una página, se usa el título para el marcador.

> [!NOTE]
> Es posible que no pueda escribir comas decimales en el campo `Price`. Para que la [validación de jQuery](https://jqueryvalidation.org/) sea compatible con configuraciones regionales distintas del inglés que usan una coma (",") en lugar de un punto decimal y formatos de fecha distintos del de Estados Unidos, debe seguir unos pasos para globalizar la aplicación. Consulte el [problema 4076 de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obtener instrucciones sobre cómo agregar la coma decimal.

La propiedad `Layout` se establece en el archivo *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

El marcado anterior establece el archivo de diseño de todos los archivos de Razor de la carpeta *Pages* en *Pages/Shared/_Layout.cshtml*. Vea [Layout](xref:razor-pages/index#layout) (Diseño) para más información.

### <a name="the-no-loccreate-page-model"></a>Modelo de página Create

Examine el modelo de página *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

El método `OnGet` inicializa cualquier estado necesario para la página. La página `Page` no tiene ningún estado que inicializar, de modo que se devuelve Create. Más adelante en el tutorial veremos el estado de inicialización del método `OnGet`. El método `Page` crea un objeto `PageResult` que representa la página *Create.cshtml*.

La propiedad `Movie` usa el atributo [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> para participar en el [enlace de modelos](xref:mvc/models/model-binding). Cuando el formulario de `Movie` publica los valores del formulario, el tiempo de ejecución de ASP.NET Core enlaza los valores publicados con el modelo Create.

El método `OnPostAsync` se ejecuta cuando la página publica los datos del formulario:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Si hay algún error de modelo, se vuelve a mostrar el formulario, junto con los datos del formulario publicados. La mayoría de los errores de modelo se pueden capturar en el cliente antes de que se publique el formulario. Un ejemplo de un error de modelo consiste en publicar un valor para el campo de fecha que no se puede convertir en una fecha. Más adelante en el tutorial, hablaremos de la validación del lado cliente y de la validación de modelos.

Si no hay ningún error de modelo, los datos se guardan y el explorador se redirige a la página Index.

### <a name="the-no-loccreate-no-locrazor-page"></a>La página Create de una instancia de Razor Pages

Examine el archivo de instancia de Razor Pages *Pages/Movies/Create.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas:

![Vista de VS17 de la página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obtener más información sobre los asistentes de etiquetas como `<form method="post">`, consulte [Asistentes de etiquetas en ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Visual Studio para Mac muestra la etiqueta `<form method="post">` con una fuente negrita diferenciada que se aplica a los asistentes de etiquetas.

---

El elemento `<form method="post">` es un [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper). El asistente de etiquetas de formulario incluye automáticamente un [token antifalsificación](xref:security/anti-request-forgery).

El motor de scaffolding crea un marcado de Razor para cada campo del modelo (excepto el identificador) similar al siguiente:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Los [asistentes de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` y `<span asp-validation-for`) muestran errores de validación. La validación se trata con más detalle en un punto posterior de esta serie.

El [asistente de etiquetas](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera el título de la etiqueta y el atributo `[for]` para la propiedad `Title`.

El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado del cliente.

## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Anterior: Adición de un modelo](xref:tutorials/razor-pages/model)
> [Siguiente: Trabajo con una base de datos](xref:tutorials/razor-pages/sql)

::: moniker-end
