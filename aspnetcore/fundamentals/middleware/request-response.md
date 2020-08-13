---
title: Operaciones de solicitud y respuesta en ASP.NET Core
author: jkotalik
description: Aprenda a leer el cuerpo de la solicitud y a escribir el cuerpo de respuesta en ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: da863ac5ecf649adffe8a3d13838be2ac1f748c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016965"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operaciones de solicitud y respuesta en ASP.NET Core

Por [Justin Kotalik](https://github.com/jkotalik)

En este artículo se explica cómo leer el cuerpo de la solicitud y escribir el cuerpo de respuesta. El código para estas operaciones podría ser necesario al escribir middleware. Fuera de la escritura de middleware, el código personalizado no suele ser necesario porque las operaciones se controlan mediante MVC and Razor Pages.

Hay dos abstracciones para los cuerpos de solicitud y respuesta: <xref:System.IO.Stream> y <xref:System.IO.Pipelines.Pipe>. En la lectura de solicitudes, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> es <xref:System.IO.Stream> y `HttpRequest.BodyReader` es <xref:System.IO.Pipelines.PipeReader>. En la escritura de respuestas, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> es <xref:System.IO.Stream> y `HttpResponse.BodyWriter` es <xref:System.IO.Pipelines.PipeWriter>.

Se recomienda el uso de [canalizaciones](/dotnet/standard/io/pipelines) por encima de las secuencias. Las secuencias pueden ser más fáciles de usar en el caso de algunas operaciones sencillas, pero las canalizaciones son más ventajosas para el rendimiento y son más fáciles de usar en la mayoría de los casos. ASP.NET Core está empezando a usar internamente canalizaciones en lugar de secuencias. Ejemplos:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Las secuencias no se quitan del marco. Se seguirán usando en todo .NET, y además muchos tipos de secuencias no tienen equivalentes de canalización, como `FileStreams` y `ResponseCompression`.

## <a name="stream-examples"></a>Ejemplos de secuencias

Imagine que el objetivo es crear un middleware que lee el cuerpo de la solicitud entero como una lista de cadenas, que se divide en nuevas líneas. Una implementación de secuencias sencilla podría parecerse al ejemplo siguiente:

> [!WARNING]
> El código siguiente:
> * Se usa para describir los problemas que se producen al no usar una canalización para leer el cuerpo de la solicitud.
> * No está pensado para usarse en aplicaciones de producción.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Este código funciona, pero hay algunos problemas:

* Antes de realizar la anexión a `StringBuilder`, en el ejemplo se crea otra cadena (`encodedString`) que se desecha inmediatamente. Este proceso se produce con todos los bytes de la secuencia, por lo que el resultado es la asignación de memoria adicional del tamaño del cuerpo de la solicitud entera.
* En el ejemplo se lee toda la cadena antes de la división en nuevas líneas. Sería más eficaz buscar nuevas líneas en la matriz de bytes.

En este ejemplo se corrigen algunos de los problemas anteriores:

> [!WARNING]
> El código siguiente:
> * Se usa para describir las soluciones a algunos problemas en el código anterior, pero no todos.
> * No está pensado para usarse en aplicaciones de producción.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Este ejemplo anterior:

* No se almacena en búfer todo el cuerpo de la solicitud en `StringBuilder` a menos que no haya ningún carácter de nueva línea.
* No se llama a `Split` en la cadena.

Sin embargo, todavía hay algunos problemas:

* Si los caracteres de nueva línea están dispersos, gran parte del cuerpo de la solicitud se almacena en búfer en la cadena.
* El código sigue creando cadenas (`remainingString`) y agrega al búfer de cadenas, lo que resulta en una asignación adicional.

Estos problemas se pueden corregir, pero el código se vuelve cada vez más complicado y las mejoras son pocas. Las canalizaciones ofrecen una manera de resolver estos problemas con una complejidad mínima del código.

## <a name="pipelines"></a>Canalizaciones

En el siguiente ejemplo se describe cómo abordar el mismo escenario usando un objeto [PipeReader](/dotnet/standard/io/pipelines#pipe):

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

En este ejemplo se solucionan muchos problemas que tenían las implementaciones de secuencias:

* No es necesario un búfer de cadenas porque `PipeReader` controla los bytes que no se han usado.
* Las cadenas codificadas se agregan directamente a la lista de cadenas devueltas.
* Aparte de la llamada a `ToArray` y de la memoria que usa la cadena, la creación de cadenas es de libre asignación.

## <a name="adapters"></a>Adaptadores

Las propiedades `Body`, `BodyReader` y `BodyWriter` están disponibles para `HttpRequest` y `HttpResponse`. Al establecer `Body` en una secuencia diferente, un nuevo conjunto de adaptadores se adaptan automáticamente al tipo del otro. Si establece `HttpRequest.Body` en una nueva secuencia, `HttpRequest.BodyReader` se establece automáticamente en un nuevo objeto `PipeReader` que encapsula a `HttpRequest.Body`.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` se usa para indicar que los encabezados no se pueden modificar y para ejecutar devoluciones de llamada `OnStarting`. Al usar Kestrel como servidor, si se llama a `StartAsync` antes de usar el objeto `PipeReader`, se garantiza que la memoria que devuelve `GetMemory` pertenezca al objeto interno <xref:System.IO.Pipelines.Pipe> de Kestrel en lugar de a un búfer externo.

## <a name="additional-resources"></a>Recursos adicionales

* [System.IO.Pipelines en .NET](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
