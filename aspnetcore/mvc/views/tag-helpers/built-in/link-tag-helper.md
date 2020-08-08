---
title: Asistente de etiquetas de vínculo en ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra los atributos del asistente de etiquetas de vínculo de ASP.NET Core y el papel que desempeña cada atributo al ampliar el comportamiento de la etiqueta de vínculo de código HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7487bc64f8e20f85ef95e5389409c0194ec31337
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018564"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Asistente de etiquetas de vínculo en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

El [asistente de etiquetas de vínculo](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) genera un vínculo a un archivo CSS principal o de reserva. Normalmente, el archivo CSS principal se encuentra en una red [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

El asistente de etiquetas de vínculo permite especificar una red CDN para el archivo CSS y una reserva cuando dicha red no está disponible. El asistente de etiquetas de vínculo proporciona la ventaja de rendimiento de una red CDN con la solidez del hospedaje local.

En el Razor marcado siguiente se muestra el `head` elemento de un archivo de diseño creado con la plantilla de aplicación web de ASP.net Core:

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Lo siguiente es código HTML representado del código anterior (en un entorno que no es de desarrollo):

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

En el código anterior, el asistente de etiquetas de vínculo generó el elemento `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` y el siguiente código JavaScript que se usa para comprobar el archivo *bootstrap.min.css* solicitado está disponible en la red CDN. En este caso, el archivo CSS estaba disponible, por lo que el asistente de etiquetas generó el elemento `<link />` con el archivo CSS de la red CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Atributos del asistente de etiquetas de vínculo utilizados habitualmente

Consulte [Asistente de etiquetas de vínculo](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) para todos los atributos, propiedades y métodos del asistente de etiquetas de vínculo.

### <a name="href"></a>href

Dirección preferida del recurso vinculado. La dirección se pasa al código HTML generado en todos los casos.

### <a name="asp-fallback-href"></a>asp-fallback-href

La dirección URL de una hoja de estilos CSS en la que se va a realizar la reserva en caso de error en la dirección URL principal.

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

El nombre de clase definido en la hoja de estilos que se va a usar para la prueba de reserva. Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

El nombre de la propiedad de CSS que se va a usar para la prueba de reserva. Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

El valor de la propiedad de CSS que se va a usar para la prueba de reserva. Para obtener más información, vea <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
