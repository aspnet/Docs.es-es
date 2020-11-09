---
title: Operaciones de solicitud y respuesta en ASP.NET Core
author: jkotalik
description: Aprenda a leer el cuerpo de la solicitud y a escribir el cuerpo de respuesta en ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/request-response
ms.openlocfilehash: cc701343cb3859f0f76ebc62bd54aa2e4431d522
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061033"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="5f461-103">Operaciones de solicitud y respuesta en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f461-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="5f461-104">Por [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5f461-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="5f461-105">En este artículo se explica cómo leer el cuerpo de la solicitud y escribir el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="5f461-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="5f461-106">El código para estas operaciones podría ser necesario al escribir middleware.</span><span class="sxs-lookup"><span data-stu-id="5f461-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="5f461-107">Fuera de la escritura de middleware, el código personalizado no suele ser necesario porque las operaciones se controlan mediante MVC and Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5f461-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="5f461-108">Hay dos abstracciones para los cuerpos de solicitud y respuesta: <xref:System.IO.Stream> y <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="5f461-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="5f461-109">En la lectura de solicitudes, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> es <xref:System.IO.Stream> y `HttpRequest.BodyReader` es <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="5f461-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="5f461-110">En la escritura de respuestas, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> es <xref:System.IO.Stream> y `HttpResponse.BodyWriter` es <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="5f461-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="5f461-111">Se recomienda el uso de [canalizaciones](/dotnet/standard/io/pipelines) por encima de las secuencias.</span><span class="sxs-lookup"><span data-stu-id="5f461-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="5f461-112">Las secuencias pueden ser más fáciles de usar en el caso de algunas operaciones sencillas, pero las canalizaciones son más ventajosas para el rendimiento y son más fáciles de usar en la mayoría de los casos.</span><span class="sxs-lookup"><span data-stu-id="5f461-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="5f461-113">ASP.NET Core está empezando a usar internamente canalizaciones en lugar de secuencias.</span><span class="sxs-lookup"><span data-stu-id="5f461-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="5f461-114">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="5f461-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="5f461-115">Las secuencias no se quitan del marco.</span><span class="sxs-lookup"><span data-stu-id="5f461-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="5f461-116">Se seguirán usando en todo .NET, y además muchos tipos de secuencias no tienen equivalentes de canalización, como `FileStreams` y `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="5f461-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="5f461-117">Ejemplos de secuencias</span><span class="sxs-lookup"><span data-stu-id="5f461-117">Stream examples</span></span>

<span data-ttu-id="5f461-118">Imagine que el objetivo es crear un middleware que lee el cuerpo de la solicitud entero como una lista de cadenas, que se divide en nuevas líneas.</span><span class="sxs-lookup"><span data-stu-id="5f461-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="5f461-119">Una implementación de secuencias sencilla podría parecerse al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="5f461-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="5f461-120">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5f461-120">The following code:</span></span>
> * <span data-ttu-id="5f461-121">Se usa para describir los problemas que se producen al no usar una canalización para leer el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="5f461-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="5f461-122">No está pensado para usarse en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="5f461-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="5f461-123">Este código funciona, pero hay algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="5f461-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="5f461-124">Antes de realizar la anexión a `StringBuilder`, en el ejemplo se crea otra cadena (`encodedString`) que se desecha inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="5f461-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="5f461-125">Este proceso se produce con todos los bytes de la secuencia, por lo que el resultado es la asignación de memoria adicional del tamaño del cuerpo de la solicitud entera.</span><span class="sxs-lookup"><span data-stu-id="5f461-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="5f461-126">En el ejemplo se lee toda la cadena antes de la división en nuevas líneas.</span><span class="sxs-lookup"><span data-stu-id="5f461-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="5f461-127">Sería más eficaz buscar nuevas líneas en la matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="5f461-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="5f461-128">En este ejemplo se corrigen algunos de los problemas anteriores:</span><span class="sxs-lookup"><span data-stu-id="5f461-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="5f461-129">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5f461-129">The following code:</span></span>
> * <span data-ttu-id="5f461-130">Se usa para describir las soluciones a algunos problemas en el código anterior, pero no todos.</span><span class="sxs-lookup"><span data-stu-id="5f461-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="5f461-131">No está pensado para usarse en aplicaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="5f461-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="5f461-132">Este ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="5f461-132">This preceding example:</span></span>

* <span data-ttu-id="5f461-133">No se almacena en búfer todo el cuerpo de la solicitud en `StringBuilder` a menos que no haya ningún carácter de nueva línea.</span><span class="sxs-lookup"><span data-stu-id="5f461-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="5f461-134">No se llama a `Split` en la cadena.</span><span class="sxs-lookup"><span data-stu-id="5f461-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="5f461-135">Sin embargo, todavía hay algunos problemas:</span><span class="sxs-lookup"><span data-stu-id="5f461-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="5f461-136">Si los caracteres de nueva línea están dispersos, gran parte del cuerpo de la solicitud se almacena en búfer en la cadena.</span><span class="sxs-lookup"><span data-stu-id="5f461-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="5f461-137">El código sigue creando cadenas (`remainingString`) y agrega al búfer de cadenas, lo que resulta en una asignación adicional.</span><span class="sxs-lookup"><span data-stu-id="5f461-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="5f461-138">Estos problemas se pueden corregir, pero el código se vuelve cada vez más complicado y las mejoras son pocas.</span><span class="sxs-lookup"><span data-stu-id="5f461-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="5f461-139">Las canalizaciones ofrecen una manera de resolver estos problemas con una complejidad mínima del código.</span><span class="sxs-lookup"><span data-stu-id="5f461-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="5f461-140">Canalizaciones</span><span class="sxs-lookup"><span data-stu-id="5f461-140">Pipelines</span></span>

<span data-ttu-id="5f461-141">En el siguiente ejemplo se describe cómo abordar el mismo escenario usando un objeto [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="5f461-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="5f461-142">En este ejemplo se solucionan muchos problemas que tenían las implementaciones de secuencias:</span><span class="sxs-lookup"><span data-stu-id="5f461-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="5f461-143">No es necesario un búfer de cadenas porque `PipeReader` controla los bytes que no se han usado.</span><span class="sxs-lookup"><span data-stu-id="5f461-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="5f461-144">Las cadenas codificadas se agregan directamente a la lista de cadenas devueltas.</span><span class="sxs-lookup"><span data-stu-id="5f461-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="5f461-145">Aparte de la llamada a `ToArray` y de la memoria que usa la cadena, la creación de cadenas es de libre asignación.</span><span class="sxs-lookup"><span data-stu-id="5f461-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="5f461-146">Adaptadores</span><span class="sxs-lookup"><span data-stu-id="5f461-146">Adapters</span></span>

<span data-ttu-id="5f461-147">Las propiedades `Body`, `BodyReader` y `BodyWriter` están disponibles para `HttpRequest` y `HttpResponse`.</span><span class="sxs-lookup"><span data-stu-id="5f461-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="5f461-148">Al establecer `Body` en una secuencia diferente, un nuevo conjunto de adaptadores se adaptan automáticamente al tipo del otro.</span><span class="sxs-lookup"><span data-stu-id="5f461-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="5f461-149">Si establece `HttpRequest.Body` en una nueva secuencia, `HttpRequest.BodyReader` se establece automáticamente en un nuevo objeto `PipeReader` que encapsula a `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="5f461-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="5f461-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5f461-150">StartAsync</span></span>

<span data-ttu-id="5f461-151">`HttpResponse.StartAsync` se usa para indicar que los encabezados no se pueden modificar y para ejecutar devoluciones de llamada `OnStarting`.</span><span class="sxs-lookup"><span data-stu-id="5f461-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="5f461-152">Al usar Kestrel como servidor, si se llama a `StartAsync` antes de usar el objeto `PipeReader`, se garantiza que la memoria que devuelve `GetMemory` pertenezca al objeto interno <xref:System.IO.Pipelines.Pipe> de Kestrel en lugar de a un búfer externo.</span><span class="sxs-lookup"><span data-stu-id="5f461-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f461-153">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5f461-153">Additional resources</span></span>

* [<span data-ttu-id="5f461-154">System.IO.Pipelines en .NET</span><span class="sxs-lookup"><span data-stu-id="5f461-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
