---
title: Formateadores personalizados en ASP.NET Core Web API
author: rick-anderson
description: Obtenga información sobre cómo crear y utilizar formateadores personalizados para las API web de ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 91c9c6513d7c8df671e283508ecc276768d79539
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587832"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="cef32-103">Formateadores personalizados en ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="cef32-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="cef32-104">Por [Kirk Larkin](https://twitter.com/serpent5) y [Tom Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="cef32-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="cef32-105">ASP.NET Core MVC admite el intercambio de datos en las API Web con formateadores de entrada y salida.</span><span class="sxs-lookup"><span data-stu-id="cef32-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="cef32-106">El [enlace de modelos](xref:mvc/models/model-binding) usa formateadores de entrada.</span><span class="sxs-lookup"><span data-stu-id="cef32-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="cef32-107">Los formateadores de salida se usan para [dar formato](xref:web-api/advanced/formatting)a las respuestas.</span><span class="sxs-lookup"><span data-stu-id="cef32-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="cef32-108">El marco proporciona formateadores de entrada y salida integrados para JSON y XML.</span><span class="sxs-lookup"><span data-stu-id="cef32-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="cef32-109">Proporciona un formateador de salida integrado para texto sin formato, pero no proporciona un formateador de entrada para texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="cef32-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="cef32-110">En este artículo se muestra cómo agregar compatibilidad con formatos adicionales mediante la creación de formateadores personalizados.</span><span class="sxs-lookup"><span data-stu-id="cef32-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="cef32-111">Para obtener un ejemplo de un formateador de entrada de texto sin formato personalizado, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) en github.</span><span class="sxs-lookup"><span data-stu-id="cef32-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="cef32-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cef32-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="cef32-113">Cuándo usar formateadores personalizados</span><span class="sxs-lookup"><span data-stu-id="cef32-113">When to use custom formatters</span></span>

<span data-ttu-id="cef32-114">Use un formateador personalizado para agregar compatibilidad para un tipo de contenido que no sea controlado por los formateadores integrados.</span><span class="sxs-lookup"><span data-stu-id="cef32-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="cef32-115">Información general sobre cómo utilizar a un formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="cef32-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="cef32-116">Para crear un formateador personalizado:</span><span class="sxs-lookup"><span data-stu-id="cef32-116">To create a custom formatter:</span></span>

* <span data-ttu-id="cef32-117">Para serializar los datos que se envían al cliente, cree una clase de formateador de salida.</span><span class="sxs-lookup"><span data-stu-id="cef32-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="cef32-118">Para deserializar los datos recibidos del cliente, cree una clase de formateador de entrada.</span><span class="sxs-lookup"><span data-stu-id="cef32-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="cef32-119">Agregue instancias de clases de formateador a las `InputFormatters` `OutputFormatters` colecciones y de <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="cef32-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="cef32-120">Cómo crear una clase de formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="cef32-120">How to create a custom formatter class</span></span>

<span data-ttu-id="cef32-121">Para crear un formateador:</span><span class="sxs-lookup"><span data-stu-id="cef32-121">To create a formatter:</span></span>

* <span data-ttu-id="cef32-122">Derive la clase de la clase base adecuada.</span><span class="sxs-lookup"><span data-stu-id="cef32-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="cef32-123">La aplicación de ejemplo se deriva de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> y <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="cef32-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="cef32-124">Especifique tipos de medios válidos y codificaciones en el constructor.</span><span class="sxs-lookup"><span data-stu-id="cef32-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="cef32-125">Invalide los métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> y <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.</span><span class="sxs-lookup"><span data-stu-id="cef32-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="cef32-126">Invalide los métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> y `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="cef32-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="cef32-127">En el código siguiente se muestra la `VcardOutputFormatter` clase del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="cef32-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="cef32-128">Derivar de la clase base adecuada</span><span class="sxs-lookup"><span data-stu-id="cef32-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="cef32-129">En el caso de los tipos de medios de texto (por ejemplo, vCard), deriven de la <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> clase base o.</span><span class="sxs-lookup"><span data-stu-id="cef32-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="cef32-130">En el caso de los tipos binarios, deriven de la <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> clase base o.</span><span class="sxs-lookup"><span data-stu-id="cef32-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="cef32-131">Especificar las codificaciones y los tipos de medios válidos</span><span class="sxs-lookup"><span data-stu-id="cef32-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="cef32-132">En el constructor, especifique tipos de medios y codificaciones válidos. Para ello, agréguelos a las colecciones `SupportedMediaTypes` y `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="cef32-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="cef32-133">Una clase de formateador **no** puede usar la inserción de constructores para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="cef32-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="cef32-134">Por ejemplo, `ILogger<VcardOutputFormatter>` no se puede agregar como parámetro al constructor.</span><span class="sxs-lookup"><span data-stu-id="cef32-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="cef32-135">Para obtener acceso a los servicios, utilice el objeto de contexto que se pasa a los métodos.</span><span class="sxs-lookup"><span data-stu-id="cef32-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="cef32-136">Un ejemplo de código de este artículo y el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) muestran cómo hacerlo.</span><span class="sxs-lookup"><span data-stu-id="cef32-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="cef32-137">Invalidar CanReadType y CanWriteType</span><span class="sxs-lookup"><span data-stu-id="cef32-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="cef32-138">Especifique el tipo que se va a deserializar en o en el que se va a serializar mediante la invalidación de los `CanReadType` `CanWriteType` métodos o.</span><span class="sxs-lookup"><span data-stu-id="cef32-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="cef32-139">Por ejemplo, la creación de texto vCard a partir de un `Contact` tipo y viceversa.</span><span class="sxs-lookup"><span data-stu-id="cef32-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="cef32-140">Método CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="cef32-140">The CanWriteResult method</span></span>

<span data-ttu-id="cef32-141">En algunos escenarios, `CanWriteResult` debe reemplazarse en lugar de `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="cef32-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="cef32-142">Use `CanWriteResult` si las condiciones siguientes son verdaderas:</span><span class="sxs-lookup"><span data-stu-id="cef32-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="cef32-143">El método de acción devuelve una clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="cef32-143">The action method returns a model class.</span></span>
* <span data-ttu-id="cef32-144">Hay clases derivadas que pueden obtenerse en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="cef32-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="cef32-145">La clase derivada devuelta por la acción debe conocerse en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="cef32-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="cef32-146">Por ejemplo, supongamos que el método de acción:</span><span class="sxs-lookup"><span data-stu-id="cef32-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="cef32-147">Signature devuelve un `Person` tipo.</span><span class="sxs-lookup"><span data-stu-id="cef32-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="cef32-148">Puede devolver un `Student` `Instructor` tipo o que deriva de `Person` .</span><span class="sxs-lookup"><span data-stu-id="cef32-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="cef32-149">Para que el formateador solo controle `Student` los objetos, compruebe el tipo de <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> en el objeto de contexto proporcionado al `CanWriteResult` método.</span><span class="sxs-lookup"><span data-stu-id="cef32-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="cef32-150">Cuando el método de acción devuelve `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="cef32-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="cef32-151">No es necesario usar `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="cef32-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="cef32-152">El `CanWriteType` método recibe el tipo en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="cef32-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="cef32-153">Invalidar ReadRequestBodyAsync y WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="cef32-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="cef32-154">La deserialización o serialización se realiza en `ReadRequestBodyAsync` o `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="cef32-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="cef32-155">En el ejemplo siguiente se muestra cómo obtener servicios del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="cef32-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="cef32-156">Los servicios no se pueden obtener a partir de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="cef32-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="cef32-157">Cómo configurar MVC para usar a un formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="cef32-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="cef32-158">Para utilizar un formateador personalizado, debe agregar una instancia de la clase de formateador a la colección `InputFormatters` o `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="cef32-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="cef32-159">Los formateadores se evalúan en el orden en que se insertaron.</span><span class="sxs-lookup"><span data-stu-id="cef32-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="cef32-160">El primero de ellos tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="cef32-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="cef32-161">La `VcardInputFormatter` clase completa</span><span class="sxs-lookup"><span data-stu-id="cef32-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="cef32-162">En el código siguiente se muestra la `VcardInputFormatter` clase del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="cef32-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="cef32-163">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="cef32-163">Test the app</span></span>

<span data-ttu-id="cef32-164">[Ejecute la aplicación de ejemplo para este artículo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples), que implementa formateadores de entrada y salida básicos de vCard.</span><span class="sxs-lookup"><span data-stu-id="cef32-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="cef32-165">La aplicación Lee y escribe vCards similares a las siguientes:</span><span class="sxs-lookup"><span data-stu-id="cef32-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="cef32-166">Para ver la salida de vCard, ejecute la aplicación y envíe una solicitud GET con el encabezado Accept `text/vcard` a `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="cef32-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="cef32-167">Para agregar una tarjeta vCard a la colección en memoria de contactos:</span><span class="sxs-lookup"><span data-stu-id="cef32-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="cef32-168">Envíe una `Post` solicitud a `/api/contacts` con una herramienta como Postman.</span><span class="sxs-lookup"><span data-stu-id="cef32-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="cef32-169">Establezca el encabezado `Content-Type` en `text/vcard`.</span><span class="sxs-lookup"><span data-stu-id="cef32-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="cef32-170">Establezca `vCard` texto en el cuerpo, con el formato del ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="cef32-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cef32-171">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="cef32-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
