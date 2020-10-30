---
title: Asistente de etiquetas parciales en ASP.NET Core
author: scottaddie
description: Conozca el asistente de etiquetas parciales en ASP.NET Core y el rol que desempeña cada uno de sus atributos a la hora de representar una vista parcial.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061436"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Asistente de etiquetas parciales en ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie)

Para obtener información general sobre asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Información general

La aplicación auxiliar de etiquetas parciales se usa para representar una [vista parcial](xref:mvc/views/partial) en Razor páginas y aplicaciones MVC. Tenga en cuenta lo siguiente:

* Es necesario ASP.NET Core 2.1 o una versión posterior.
* Es una alternativa a la [sintaxis del asistente de HTML](xref:mvc/views/partial#reference-a-partial-view).
* Presenta la vista parcial de forma asincrónica.

Las opciones del asistente de HTML para representar una vista parcial son estas:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

En los ejemplos de todo este documento se usa el modelo *Product* :

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Ahora pasaremos a ver una relación de los atributos del asistente de etiquetas parciales.

## <a name="name"></a>name

El atributo `name` es necesario. Señala el nombre o la ruta de acceso de la vista parcial que se va a representar. Cuando se indica el nombre de una vista parcial, se inicia el proceso de [detección de vista](xref:mvc/views/overview#view-discovery). Este proceso se omite cuando se proporciona una ruta de acceso explícita. Para conocer todos los valores `name` aceptables, consulte [Detección de vistas parciales](xref:mvc/views/partial#partial-view-discovery).

En el siguiente marcado se usa una ruta de acceso explícita, lo que indica que *_ProductPartial.cshtml* debe cargarse desde la carpeta *Shared* . Mediante el atributo [for](#for), se pasa un modelo a la vista parcial para el enlace.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>para

El atributo `for` asigna una [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) para que se evalúe según el modelo actual. `ModelExpression` deduce la sintaxis de `@Model.`. Por ejemplo, se puede usar `for="Product"` en lugar de `for="@Model.Product"`. Este comportamiento predeterminado de deducción queda invalidado si se usa el símbolo `@` para definir una expresión insertada.

El siguiente marcado carga *_ProductPartial.cshtml* :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

La vista parcial se enlaza a la propiedad `Product` del modelo de página asociado correspondiente:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

El atributo `model` asigna una instancia de modelo para pasarla a la vista parcial. El atributo `model` no se puede usar con el atributo [for](#for).

En el siguiente código de marcado, se crea un objeto `Product` y se pasa al atributo `model` para el enlace:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

El atributo `view-data` asigna un [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) para pasarlo a la vista parcial. El siguiente marcado hace que toda la colección ViewData esté accesible para la vista parcial:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

En el código anterior, el valor de clave `IsNumberReadOnly` está establecido en `true` y se ha agregado a la colección ViewData. Por tanto, `ViewData["IsNumberReadOnly"]` estará accesible dentro de la vista parcial siguiente:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

En este ejemplo, el valor de `ViewData["IsNumberReadOnly"]` determina si el campo *Number* se muestra como de solo lectura.

## <a name="migrate-from-an-html-helper"></a>Migración desde un asistente de HTML

Tenga en cuenta el siguiente ejemplo del asistente de HTML asincrónico. Se itera y se muestra una colección de productos. Según el primer parámetro del método `PartialAsync`, se carga la vista parcial *_ProductPartial.cshtml* . Se pasa una instancia del modelo `Product` a la vista parcial para el enlace.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

El siguiente asistente de etiquetas parciales logra el mismo comportamiento de representación asincrónica que el asistente de HTML `PartialAsync`. El atributo `model` tiene asignada una instancia del modelo `Product` para el enlace a la vista parcial.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
