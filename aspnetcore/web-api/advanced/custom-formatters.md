---
title: Formateadores personalizados en ASP.NET Core Web API
author: rick-anderson
description: Obtenga información sobre cómo crear y utilizar formateadores personalizados para las API web de ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: ecf233273a28df9b2d35edf3264b8c73b16759e5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021879"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formateadores personalizados en ASP.NET Core Web API

Por [Kirk Larkin](https://twitter.com/serpent5) y [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC admite el intercambio de datos en las API Web con formateadores de entrada y salida. El [enlace de modelos](xref:mvc/models/model-binding) usa formateadores de entrada. Los formateadores de salida se usan para [dar formato](xref:web-api/advanced/formatting)a las respuestas.

El marco proporciona formateadores de entrada y salida integrados para JSON y XML. Proporciona un formateador de salida integrado para texto sin formato, pero no proporciona un formateador de entrada para texto sin formato.

En este artículo se muestra cómo agregar compatibilidad con formatos adicionales mediante la creación de formateadores personalizados. Para obtener un ejemplo de un formateador de entrada de texto sin formato personalizado, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) en github.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Cuándo usar formateadores personalizados

Use un formateador personalizado para agregar compatibilidad para un tipo de contenido que no sea controlado por los formateadores integrados.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Información general sobre cómo utilizar a un formateador personalizado

Para crear un formateador personalizado:

* Para serializar los datos que se envían al cliente, cree una clase de formateador de salida.
* Para deserializar los datos recibidos del cliente, cree una clase de formateador de entrada.
* Agregue instancias de clases de formateador a las `InputFormatters` `OutputFormatters` colecciones y de <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Cómo crear una clase de formateador personalizado

Para crear un formateador:

* Derive la clase de la clase base adecuada. La aplicación de ejemplo se deriva de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> y <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Especifique tipos de medios válidos y codificaciones en el constructor.
* Invalide los métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> y <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Invalide los métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> y `WriteResponseBodyAsync`.

En el código siguiente se muestra la `VcardOutputFormatter` clase del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Derivar de la clase base adecuada

En el caso de los tipos de medios de texto (por ejemplo, vCard), deriven de la <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> clase base o.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

En el caso de los tipos binarios, deriven de la <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> clase base o.

### <a name="specify-valid-media-types-and-encodings"></a>Especificar las codificaciones y los tipos de medios válidos

En el constructor, especifique tipos de medios y codificaciones válidos. Para ello, agréguelos a las colecciones `SupportedMediaTypes` y `SupportedEncodings`.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Una clase de formateador **no** puede usar la inserción de constructores para sus dependencias. Por ejemplo, `ILogger<VcardOutputFormatter>` no se puede agregar como parámetro al constructor. Para obtener acceso a los servicios, utilice el objeto de contexto que se pasa a los métodos. Un ejemplo de código de este artículo y el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) muestran cómo hacerlo.

### <a name="override-canreadtype-and-canwritetype"></a>Invalidar CanReadType y CanWriteType

Especifique el tipo que se va a deserializar en o en el que se va a serializar mediante la invalidación de los `CanReadType` `CanWriteType` métodos o. Por ejemplo, la creación de texto vCard a partir de un `Contact` tipo y viceversa.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>Método CanWriteResult

En algunos escenarios, `CanWriteResult` debe reemplazarse en lugar de `CanWriteType` . Use `CanWriteResult` si las condiciones siguientes son verdaderas:

* El método de acción devuelve una clase de modelo.
* Hay clases derivadas que pueden obtenerse en tiempo de ejecución.
* La clase derivada devuelta por la acción debe conocerse en tiempo de ejecución.

Por ejemplo, supongamos que el método de acción:

* Signature devuelve un `Person` tipo.
* Puede devolver un `Student` `Instructor` tipo o que deriva de `Person` . 

Para que el formateador solo controle `Student` los objetos, compruebe el tipo de <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> en el objeto de contexto proporcionado al `CanWriteResult` método. Cuando el método de acción devuelve `IActionResult` :

* No es necesario usar `CanWriteResult` .
* El `CanWriteType` método recibe el tipo en tiempo de ejecución.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Invalidar ReadRequestBodyAsync y WriteResponseBodyAsync

La deserialización o serialización se realiza en `ReadRequestBodyAsync` o `WriteResponseBodyAsync` . En el ejemplo siguiente se muestra cómo obtener servicios del contenedor de inserción de dependencias. Los servicios no se pueden obtener a partir de parámetros de constructor.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Cómo configurar MVC para usar a un formateador personalizado

Para utilizar un formateador personalizado, debe agregar una instancia de la clase de formateador a la colección `InputFormatters` o `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Los formateadores se evalúan en el orden en que se insertaron. El primero de ellos tiene prioridad.

## <a name="the-complete-vcardinputformatter-class"></a>La `VcardInputFormatter` clase completa

En el código siguiente se muestra la `VcardInputFormatter` clase del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Prueba de la aplicación

[Ejecute la aplicación de ejemplo para este artículo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), que implementa formateadores de entrada y salida básicos de vCard. La aplicación Lee y escribe vCards similares a las siguientes:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Para ver la salida de vCard, ejecute la aplicación y envíe una solicitud GET con el encabezado Accept `text/vcard` a `https://localhost:5001/api/contacts` .

Para agregar una tarjeta vCard a la colección en memoria de contactos:

* Envíe una `Post` solicitud a `/api/contacts` con una herramienta como Postman.
* Establezca el encabezado `Content-Type` en `text/vcard`.
* Establezca `vCard` texto en el cuerpo, con el formato del ejemplo anterior.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
