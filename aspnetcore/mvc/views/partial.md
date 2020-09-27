---
title: Vistas parciales en ASP.NET Core
author: ardalis
description: Descubra cómo usar las vistas parciales para dividir los archivos de marcado de gran tamaño y reducir la duplicación de marcado común en las páginas web en aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: bb75d8b7d78c82c9c32605ac645b8895fdfb78e3
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393657"
---
# <a name="partial-views-in-aspnet-core"></a>Vistas parciales en ASP.NET Core

Por [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Scott Sauber](https://twitter.com/scottsauber)

Una vista parcial es un [Razor](xref:mvc/views/razor) archivo de marcado (*. cshtml*) sin una [`@page`](xref:mvc/views/razor#page) Directiva que representa la salida HTML *dentro* de otra salida representada de un archivo de marcado.

::: moniker range=">= aspnetcore-2.1"

El término *vista parcial* se usa al desarrollar una aplicación MVC, donde los archivos de marcado se denominan *vistas*, o una Razor aplicación de páginas, donde los archivos de marcado se denominan *páginas*. Este tema hace referencia de forma genérica a las páginas de vistas y páginas de MVC Razor como *archivos de marcado*.

::: moniker-end

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>¿Cuándo se usan las vistas parciales?

Las vistas parciales son una forma eficaz de:

* Dividir los archivos de marcado grandes en componentes más pequeños.

  En un gran archivo de marcado complejo formado por varias partes lógicas, se ofrece la ventaja de trabajar con cada pieza aislada en una vista parcial. El código del archivo de marcado es fácil de administrar porque el marcado solo contiene la estructura general de la página y las referencias a las vistas parciales.
* Reducir la duplicación de contenido de marcado común en todos los archivos de marcado.

  Cuando se usan los mismos elementos de marcado en todos los archivos de marcado, una vista parcial elimina la duplicación del contenido de marcado en un archivo de vista parcial. Cuando se cambia el marcado en la vista parcial, actualiza la salida representada de los archivos de marcado que utilizan la vista parcial.

Las vistas parciales no deben utilizarse para mantener los elementos de diseño comunes. Se deben especificar elementos de diseño comunes en los archivos [_Layout.cshtml](xref:mvc/views/layout).

No utilice una vista parcial donde se requiera la ejecución de código o lógica de representación compleja para representar el marcado. En lugar de una vista parcial, use un [componente de vista](xref:mvc/views/view-components).

## <a name="declare-partial-views"></a>Declaración de vistas parciales

::: moniker range=">= aspnetcore-2.0"

Una vista parcial es un archivo de marcado *. cshtml* sin una [`@page`](xref:mvc/views/razor#page) Directiva que se mantiene en la carpeta *views* (MVC) o en la carpeta *pages* ( Razor pages).

En ASP.NET Core MVC, la clase <xref:Microsoft.AspNetCore.Mvc.ViewResult> de un controlador es capaz de devolver una vista o una vista parcial. En Razor las páginas, un <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> puede devolver una vista parcial representada como un <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objeto. La referencia y representación de vistas parciales se describe en la sección [Referencia a una vista parcial](#reference-a-partial-view).

A diferencia de la representación de páginas o vistas de MVC, una vista parcial no ejecuta *_ViewStart.cshtml*. Para más información sobre *_ViewStart.cshtml*, vea <xref:mvc/views/layout>.

Los nombres de archivo de las vistas parciales suelen comenzar con un guión bajo (`_`). Esta convención de nomenclatura no es obligatoria, pero ayuda a diferenciar visualmente las vistas parciales de las vistas y las páginas.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Una vista parcial es un archivo de marcado *.cshtml* que se mantiene dentro de la carpeta *Vistas*.

La clase <xref:Microsoft.AspNetCore.Mvc.ViewResult> de un controlador es capaz de devolver una vista o una vista parcial. La referencia y representación de vistas parciales se describe en la sección [Referencia a una vista parcial](#reference-a-partial-view).

A diferencia de la representación de vistas de MVC, una vista parcial no ejecuta *_ViewStart.cshtml*. Para más información sobre *_ViewStart.cshtml*, vea <xref:mvc/views/layout>.

Los nombres de archivo de las vistas parciales suelen comenzar con un guión bajo (`_`). Esta convención de nomenclatura no es obligatoria, pero ayuda a diferenciar visualmente las vistas parciales de las vistas.

::: moniker-end

## <a name="reference-a-partial-view"></a>Referencia a una vista parcial

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a>Usar una vista parcial en un Razor PageModel de páginas

En ASP.NET Core 2,0 o 2,1, el siguiente método de controlador representa la vista parcial de * \_ AuthorPartialRP. cshtml* en la respuesta:

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

En ASP.NET Core 2.2 o posterior, un método de controlador puede llamar de forma alternativa al método <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> para generar un objeto `PartialViewResult`:

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a>Uso de una vista parcial en un archivo de marcado

::: moniker range=">= aspnetcore-2.1"

Dentro de un archivo de marcado, hay varias maneras de hacer referencia a una vista parcial. Se recomienda que las aplicaciones usen uno de los siguientes métodos de representación asincrónica:

* [Asistente de etiquetas parciales](#partial-tag-helper)
* [Asistente de HTML asincrónica](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

Dentro de un archivo de marcado, hay dos formas de hacer referencia a una vista parcial:

* [Asistente de HTML asincrónica](#asynchronous-html-helper)
* [Asistente de HTML sincrónico](#synchronous-html-helper)

Se recomienda que las aplicaciones usen el [Asistente de HTML asincrónico](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Asistente de etiquetas parciales

La [aplicación auxiliar de etiquetas parciales](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requiere ASP.net Core 2,1 o posterior.

El asistente de etiquetas parciales representa contenido de forma asincrónica y usa una sintaxis similar a HTML:

```cshtml
<partial name="_PartialName" />
```

Cuando hay una extensión de archivo, el asistente de etiquetas hace referencia a una vista parcial que debe estar en la misma carpeta que el archivo de marcado que llama a la vista parcial:

```cshtml
<partial name="_PartialName.cshtml" />
```

En el ejemplo siguiente se hace referencia a una vista parcial desde la raíz de la aplicación. Las rutas de acceso que comienzan con una tilde de la ñ y una barra diagonal (`~/`) o una barra diagonal (`/`) hacen referencia a la raíz de la aplicación:

**Razor Páginas**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

En el ejemplo siguiente se hace referencia a una vista parcial con una ruta de acceso relativa:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Asistente de HTML asincrónica

Cuando se usa un asistente de HTML, el procedimiento recomendado es usar <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync` devuelve un tipo <xref:Microsoft.AspNetCore.Html.IHtmlContent> encapsulado en una clase <xref:System.Threading.Tasks.Task%601>. Para hacer referencia al método, se agrega a la llamada awaited un carácter `@` como prefijo:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Cuando la extensión del archivo está presente, el asistente de HTML hace referencia a una vista parcial que debe estar en la misma carpeta que el archivo de marcado que llama a la vista parcial:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

En el ejemplo siguiente se hace referencia a una vista parcial desde la raíz de la aplicación. Las rutas de acceso que comienzan con una tilde de la ñ y una barra diagonal (`~/`) o una barra diagonal (`/`) hacen referencia a la raíz de la aplicación:

::: moniker range=">= aspnetcore-2.1"

**Razor Páginas**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

En el ejemplo siguiente se hace referencia a una vista parcial con una ruta de acceso relativa:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

También puede representar una vista parcial con <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>. Este método no devuelve <xref:Microsoft.AspNetCore.Html.IHtmlContent>. sino que transmite por secuencias la salida representada directamente a la respuesta. Dado que el método no devuelve un resultado, se debe llamar dentro de un Razor bloque de código:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Puesto que `RenderPartialAsync` transmite contenido representado, ofrece mayor rendimiento en algunos escenarios. En situaciones críticas de rendimiento, realice pruebas comparativas de la página con ambos métodos y use el que genera una respuesta más rápida.

### <a name="synchronous-html-helper"></a>Asistente de HTML sincrónico

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> y <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> son los equivalentes sincrónicos de `PartialAsync` y `RenderPartialAsync`, respectivamente. Los equivalentes sincrónicos no son aconsejables, ya que hay escenarios donde producen interbloqueos. Se prevé la eliminación de los métodos sincrónicos en una futura versión.

> [!IMPORTANT]
> Si no necesita ejecutar código, use un [componente de vista](xref:mvc/views/view-components) en lugar de una vista parcial.

::: moniker range=">= aspnetcore-2.1"

La llamada a `Partial` o `RenderPartial` resulta en una advertencia del analizador de Visual Studio. Por ejemplo, la presencia de `Partial` genera el siguiente mensaje de advertencia:

> Use of IHtmlHelper.Partial may result in application deadlocks. Considere la posibilidad de utilizar el asistente de etiquetas &lt;parciales&gt; o IHtmlHelper.PartialAsync.

Reemplace las llamadas a `@Html.Partial` por `@await Html.PartialAsync` o a la [aplicación auxiliar de etiquetas parciales](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Para más información sobre la migración del asistente de etiquetas parciales, vea [Migración desde un asistente de HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Detección de vistas parciales

Cuando se hace referencia a una vista parcial por su nombre sin una extensión de archivo, se busca en las siguientes ubicaciones en el orden indicado:

::: moniker range=">= aspnetcore-2.1"

**Razor Páginas**

1. Carpeta de la página en ejecución actualmente
1. Gráfico de directorio por encima de la carpeta de la página
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

Las convenciones siguientes se aplican a la detección de la vista parcial:

* Se admiten diferentes vistas parciales con el mismo nombre de archivo cuando las vistas parciales están en carpetas diferentes.
* Al hacer referencia a una vista parcial por su nombre sin una extensión de archivo y la vista parcial está presente tanto en la carpeta del autor de la llamada como en la carpeta *compartida*, la vista parcial de la carpeta del autor de la llamada proporciona la vista parcial. Si la vista parcial no está presente en la carpeta del autor de la llamada, se proporciona la vista parcial desde la carpeta *compartida*. Las vistas parciales de la carpeta *compartida* se denominan *vistas parciales compartidas* o *vistas parciales predeterminadas*.
* Las vistas parciales se pueden *encadenar* &mdash; una vista parcial puede llamar a otra vista parcial si las llamadas no forman una referencia circular. Las rutas de acceso relativas siempre guardan relación con el archivo actual, no con la raíz ni el elemento primario del archivo.

> [!NOTE]
> Un [Razor](xref:mvc/views/razor) `section` definido en una vista parcial es invisible para los archivos de marcado primarios. La `section` solo es visible para la vista parcial en la que está definida.

## <a name="access-data-from-partial-views"></a>Acceso a datos desde vistas parciales

Cuando se crea una instancia de una vista parcial, recibe una *copia* del diccionario `ViewData` del elemento primario. Las actualizaciones realizadas en los datos dentro de la vista parcial no se conservan en la vista principal. Los cambios de `ViewData` en una vista parcial se pierden cuando se devuelve la vista parcial.

En el ejemplo siguiente se muestra cómo pasar una instancia de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a una vista parcial:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Puede pasar un modelo a una vista parcial. El modelo puede ser un objeto personalizado. Puede pasar un modelo con `PartialAsync` (representa un bloque de contenido al autor de la llamada) o `RenderPartialAsync` (transmite el contenido a la salida):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**Razor Páginas**

El siguiente marcado de la aplicación de ejemplo proviene de la página *Pages/ArticlesRP/ReadRP.cshtml*. La página contiene dos vistas parciales. La segunda vista parcial se pasa a un modelo y `ViewData` a la vista parcial. La sobrecarga del constructor de `ViewDataDictionary` se usa para pasar un nuevo diccionario `ViewData` a la vez que conserva el diccionario `ViewData` existente.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

*Pages/Shared/_AuthorPartialRP.cshtml* es la primera vista parcial a la que hace referencia el archivo de marcado *ReadRP.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP.cshtml* es la segunda vista parcial a la que hace referencia el archivo de marcado *ReadRP.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

El marcado siguiente de la aplicación de ejemplo muestra la vista *Views/Articles/Read.cshtml*. La vista contiene dos vistas parciales. La segunda vista parcial se pasa a un modelo y `ViewData` a la vista parcial. La sobrecarga del constructor de `ViewDataDictionary` se usa para pasar un nuevo diccionario `ViewData` a la vez que conserva el diccionario `ViewData` existente.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

*Views/Shared/_AuthorPartial.cshtml* es la primera vista parcial a la que hace referencia el archivo de marcado *Read.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Views/Articles/_ArticleSection.cshtml* es la segunda vista parcial a la que hace referencia el archivo de marcado *Read.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

En tiempo de ejecución, las vistas parciales se representan en la salida representada del archivo de marcado principal, que a su vez se representa dentro de *_Layout.cshtml* compartido. La primera vista parcial representa la fecha de publicación y el nombre del autor del artículo:

> Abraham Lincoln
>
> Esta vista parcial procede de la &lt;ruta de acceso de archivo de la vista parcial compartida&gt;.
> 19/11/1863 12:00:00 a. m.

La segunda vista parcial representa las secciones del artículo:

> Índice de la sección uno: 0
>
> Puntuación de cuatro y hace siete años...
>
> Índice de la sección dos: 1
>
> Ahora nos encontramos en una verdadera guerra civil, probando...
>
> Índice de la sección tres: 2
>
> Pero, en un sentido amplio, no nos podemos dedicar...

## <a name="additional-resources"></a>Recursos adicionales

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
