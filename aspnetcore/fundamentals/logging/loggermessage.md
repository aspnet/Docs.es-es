---
title: Registro de alto rendimiento con LoggerMessage en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar LoggerMessage para crear delegados almacenables en caché que requieren menos asignaciones de objetos que los escenarios de registro de alto rendimiento.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 0224e768bd0e016eac5165dc4d9745f4b0867094
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060461"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="01400-103">Registro de alto rendimiento con LoggerMessage en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01400-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01400-104">Las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> crean delegados almacenables en caché que requieren menos asignaciones de objetos y una menor sobrecarga computacional en comparación con los [métodos de extensión del registrador](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="01400-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="01400-105">Para escenarios de registro de alto rendimiento, use el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="01400-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="01400-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> proporciona las siguientes ventajas de rendimiento frente a los métodos de extensión del registrador:</span><span class="sxs-lookup"><span data-stu-id="01400-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="01400-107">Los métodos de extensión del registrador requieren la conversión boxing de tipos de valor, como `int`, en `object`.</span><span class="sxs-lookup"><span data-stu-id="01400-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="01400-108">El patrón <xref:Microsoft.Extensions.Logging.LoggerMessage> impide la conversión boxing mediante métodos de extensión y campos <xref:System.Action> estáticos con parámetros fuertemente tipados.</span><span class="sxs-lookup"><span data-stu-id="01400-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="01400-109">Los métodos de extensión del registrador deben analizar la plantilla de mensaje (cadena de formato con nombre) cada vez que se escribe un mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="01400-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> solo necesita analizar una vez una plantilla cuando se define el mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="01400-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01400-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="01400-112">La aplicación de ejemplo muestra las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema de seguimiento de citas básico.</span><span class="sxs-lookup"><span data-stu-id="01400-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="01400-113">La aplicación agrega y elimina citas mediante una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="01400-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="01400-114">A medida que se producen estas operaciones, se generan mensajes de registro mediante el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="01400-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="01400-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="01400-115">LoggerMessage.Define</span></span>

<span data-ttu-id="01400-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegado <xref:System.Action> para registrar un mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="01400-117">Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permiten pasar hasta seis parámetros de tipo a una cadena de formato con nombre (plantilla).</span><span class="sxs-lookup"><span data-stu-id="01400-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="01400-118">La cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> es una plantilla y no una cadena interpolada.</span><span class="sxs-lookup"><span data-stu-id="01400-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="01400-119">Los marcadores de posición se rellenan en el orden en que se especifican los tipos.</span><span class="sxs-lookup"><span data-stu-id="01400-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="01400-120">Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas.</span><span class="sxs-lookup"><span data-stu-id="01400-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="01400-121">Sirven como nombres de propiedad en los datos estructurados del registro.</span><span class="sxs-lookup"><span data-stu-id="01400-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="01400-122">Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición.</span><span class="sxs-lookup"><span data-stu-id="01400-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="01400-123">Por ejemplo: `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="01400-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="01400-124">Cada mensaje de registro es un delegado <xref:System.Action> que se mantiene en un campo estático creado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="01400-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="01400-125">Por ejemplo, la aplicación de ejemplo crea un campo que describe un mensaje de registro para una solicitud GET para la página de índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="01400-126">Especifique lo siguiente para el delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="01400-127">El nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-127">The log level.</span></span>
* <span data-ttu-id="01400-128">Un identificador de evento único (<xref:Microsoft.Extensions.Logging.EventId>) con el nombre del método de extensión estático.</span><span class="sxs-lookup"><span data-stu-id="01400-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="01400-129">La plantilla de mensaje (cadena de formato con nombre).</span><span class="sxs-lookup"><span data-stu-id="01400-129">The message template (named format string).</span></span> 

<span data-ttu-id="01400-130">Una solicitud para la página de índice de la aplicación de ejemplo establece:</span><span class="sxs-lookup"><span data-stu-id="01400-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="01400-131">El nivel de registro en `Information`.</span><span class="sxs-lookup"><span data-stu-id="01400-131">Log level to `Information`.</span></span>
* <span data-ttu-id="01400-132">El identificador de evento en `1` con el nombre del método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="01400-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="01400-133">La plantilla de mensaje (cadena de formato con nombre) en una cadena.</span><span class="sxs-lookup"><span data-stu-id="01400-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="01400-134">Los almacenes de registro estructurado pueden usar el nombre de evento cuando se suministra con el identificador de evento para enriquecer el registro.</span><span class="sxs-lookup"><span data-stu-id="01400-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="01400-135">Por ejemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa el nombre de evento.</span><span class="sxs-lookup"><span data-stu-id="01400-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="01400-136">El delegado <xref:System.Action> se invoca mediante un método de extensión fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="01400-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="01400-137">El método `IndexPageRequested` registra un mensaje para una solicitud GET de página de índice en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="01400-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="01400-138">Se llama a `IndexPageRequested` en el registrador en el método `OnGetAsync` en *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="01400-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="01400-139">Inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="01400-140">Para pasar parámetros a un mensaje de registro, defina hasta seis tipos al crear el campo estático.</span><span class="sxs-lookup"><span data-stu-id="01400-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="01400-141">La aplicación de ejemplo registra una cadena cuando se agrega una cita. Para ello, define un tipo `string` para el campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="01400-142">La plantilla de mensaje de registro del delegado recibe sus valores de marcador de posición a partir de los tipos proporcionados.</span><span class="sxs-lookup"><span data-stu-id="01400-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="01400-143">La aplicación de ejemplo define un delegado para agregar una cita cuando el parámetro de la cita es `string`:</span><span class="sxs-lookup"><span data-stu-id="01400-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="01400-144">El método de extensión estático para agregar una cita, `QuoteAdded`, recibe el valor de argumento de la cita y lo pasa al delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="01400-145">En el modelo de página para la página de índice (*Pages/Index.cshtml.cs*), se llama a `QuoteAdded` para registrar el mensaje:</span><span class="sxs-lookup"><span data-stu-id="01400-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="01400-146">Inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="01400-147">La aplicación de ejemplo implementa un patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para la eliminación de la cita.</span><span class="sxs-lookup"><span data-stu-id="01400-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="01400-148">Se registra un mensaje informativo si se realiza correctamente una operación de eliminación.</span><span class="sxs-lookup"><span data-stu-id="01400-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="01400-149">Se registra un mensaje de error para una operación de eliminación si se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="01400-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="01400-150">El mensaje de registro de la operación de eliminación con error incluye el seguimiento de la pila de excepciones (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="01400-151">Observe cómo se pasa la excepción al delegado en `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="01400-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="01400-152">En el modelo de página para la página de índice, una operación correcta de eliminación de cita llama al método `QuoteDeleted` en el registrador.</span><span class="sxs-lookup"><span data-stu-id="01400-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="01400-153">Cuando no se encuentra una cita para su eliminación, se produce una excepción <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="01400-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="01400-154">La excepción se captura mediante la instrucción [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) y se registra mediante una llamada al método `QuoteDeleteFailed` en el registrador en el bloque [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="01400-155">Cuando se elimine correctamente una cita, inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="01400-156">Cuando se produzca un error en la eliminación de una cita, inspeccione la salida de la consola de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01400-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="01400-157">Tenga en cuenta que la excepción se incluye en el mensaje del registro:</span><span class="sxs-lookup"><span data-stu-id="01400-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="01400-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="01400-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="01400-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegado <xref:System.Func%601> para definir un [ámbito de registro](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="01400-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="01400-160">Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permiten pasar hasta tres parámetros de tipo a una cadena de formato con nombre (plantilla).</span><span class="sxs-lookup"><span data-stu-id="01400-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="01400-161">Al igual que sucede con el método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> es una plantilla y no una cadena interpolada.</span><span class="sxs-lookup"><span data-stu-id="01400-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="01400-162">Los marcadores de posición se rellenan en el orden en que se especifican los tipos.</span><span class="sxs-lookup"><span data-stu-id="01400-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="01400-163">Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas.</span><span class="sxs-lookup"><span data-stu-id="01400-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="01400-164">Sirven como nombres de propiedad en los datos estructurados del registro.</span><span class="sxs-lookup"><span data-stu-id="01400-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="01400-165">Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición.</span><span class="sxs-lookup"><span data-stu-id="01400-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="01400-166">Por ejemplo: `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="01400-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="01400-167">Defina un [ámbito de registro](xref:fundamentals/logging/index#log-scopes) para aplicarlo a una serie de mensajes de registro mediante el método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="01400-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="01400-168">La aplicación de ejemplo tiene un botón **Borrar todo** para eliminar todas las citas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="01400-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="01400-169">Para eliminar las citas, se van quitando de una en una.</span><span class="sxs-lookup"><span data-stu-id="01400-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="01400-170">Cada vez que se elimina una cita, se llama al método `QuoteDeleted` en el registrador.</span><span class="sxs-lookup"><span data-stu-id="01400-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="01400-171">Se agrega un ámbito de registro a estos mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="01400-172">Habilite `IncludeScopes` en la sección del registrador de consola de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="01400-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="01400-173">Para crear un ámbito de registro, agregue un campo para que contenga un delegado <xref:System.Func%601> para el ámbito.</span><span class="sxs-lookup"><span data-stu-id="01400-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="01400-174">La aplicación de ejemplo crea un campo denominado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="01400-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para crear el delegado.</span><span class="sxs-lookup"><span data-stu-id="01400-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="01400-176">Pueden especificarse hasta tres tipos para usarlos como argumentos de plantilla cuando se invoca el delegado.</span><span class="sxs-lookup"><span data-stu-id="01400-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="01400-177">La aplicación de ejemplo usa una plantilla de mensaje que incluye el número de citas eliminadas (un tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="01400-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="01400-178">Proporcione un método de extensión estático para el mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="01400-179">Incluya todos los parámetros de tipo para propiedades con nombre que aparezcan en la plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="01400-180">La aplicación de ejemplo toma un valor de número `count` de citas que se van a eliminar y devuelve `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="01400-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="01400-181">El ámbito encapsula las llamadas a la extensión de registro en un bloque [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="01400-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="01400-182">Inspeccione los mensajes de registro en la salida de la consola de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01400-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="01400-183">En el resultado siguiente se muestran tres citas eliminadas con el mensaje del ámbito de registro incluido:</span><span class="sxs-lookup"><span data-stu-id="01400-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01400-184">Las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> crean delegados almacenables en caché que requieren menos asignaciones de objetos y una menor sobrecarga computacional en comparación con los [métodos de extensión del registrador](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="01400-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="01400-185">Para escenarios de registro de alto rendimiento, use el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="01400-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="01400-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> proporciona las siguientes ventajas de rendimiento frente a los métodos de extensión del registrador:</span><span class="sxs-lookup"><span data-stu-id="01400-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="01400-187">Los métodos de extensión del registrador requieren la conversión boxing de tipos de valor, como `int`, en `object`.</span><span class="sxs-lookup"><span data-stu-id="01400-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="01400-188">El patrón <xref:Microsoft.Extensions.Logging.LoggerMessage> impide la conversión boxing mediante métodos de extensión y campos <xref:System.Action> estáticos con parámetros fuertemente tipados.</span><span class="sxs-lookup"><span data-stu-id="01400-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="01400-189">Los métodos de extensión del registrador deben analizar la plantilla de mensaje (cadena de formato con nombre) cada vez que se escribe un mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="01400-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> solo necesita analizar una vez una plantilla cuando se define el mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="01400-191">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01400-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="01400-192">La aplicación de ejemplo muestra las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema de seguimiento de citas básico.</span><span class="sxs-lookup"><span data-stu-id="01400-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="01400-193">La aplicación agrega y elimina citas mediante una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="01400-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="01400-194">A medida que se producen estas operaciones, se generan mensajes de registro mediante el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="01400-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="01400-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="01400-195">LoggerMessage.Define</span></span>

<span data-ttu-id="01400-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegado <xref:System.Action> para registrar un mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="01400-197">Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permiten pasar hasta seis parámetros de tipo a una cadena de formato con nombre (plantilla).</span><span class="sxs-lookup"><span data-stu-id="01400-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="01400-198">La cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> es una plantilla y no una cadena interpolada.</span><span class="sxs-lookup"><span data-stu-id="01400-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="01400-199">Los marcadores de posición se rellenan en el orden en que se especifican los tipos.</span><span class="sxs-lookup"><span data-stu-id="01400-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="01400-200">Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas.</span><span class="sxs-lookup"><span data-stu-id="01400-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="01400-201">Sirven como nombres de propiedad en los datos estructurados del registro.</span><span class="sxs-lookup"><span data-stu-id="01400-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="01400-202">Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición.</span><span class="sxs-lookup"><span data-stu-id="01400-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="01400-203">Por ejemplo: `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="01400-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="01400-204">Cada mensaje de registro es un delegado <xref:System.Action> que se mantiene en un campo estático creado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="01400-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="01400-205">Por ejemplo, la aplicación de ejemplo crea un campo que describe un mensaje de registro para una solicitud GET para la página de índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="01400-206">Especifique lo siguiente para el delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="01400-207">El nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-207">The log level.</span></span>
* <span data-ttu-id="01400-208">Un identificador de evento único (<xref:Microsoft.Extensions.Logging.EventId>) con el nombre del método de extensión estático.</span><span class="sxs-lookup"><span data-stu-id="01400-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="01400-209">La plantilla de mensaje (cadena de formato con nombre).</span><span class="sxs-lookup"><span data-stu-id="01400-209">The message template (named format string).</span></span> 

<span data-ttu-id="01400-210">Una solicitud para la página de índice de la aplicación de ejemplo establece:</span><span class="sxs-lookup"><span data-stu-id="01400-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="01400-211">El nivel de registro en `Information`.</span><span class="sxs-lookup"><span data-stu-id="01400-211">Log level to `Information`.</span></span>
* <span data-ttu-id="01400-212">El identificador de evento en `1` con el nombre del método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="01400-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="01400-213">La plantilla de mensaje (cadena de formato con nombre) en una cadena.</span><span class="sxs-lookup"><span data-stu-id="01400-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="01400-214">Los almacenes de registro estructurado pueden usar el nombre de evento cuando se suministra con el identificador de evento para enriquecer el registro.</span><span class="sxs-lookup"><span data-stu-id="01400-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="01400-215">Por ejemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa el nombre de evento.</span><span class="sxs-lookup"><span data-stu-id="01400-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="01400-216">El delegado <xref:System.Action> se invoca mediante un método de extensión fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="01400-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="01400-217">El método `IndexPageRequested` registra un mensaje para una solicitud GET de página de índice en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="01400-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="01400-218">Se llama a `IndexPageRequested` en el registrador en el método `OnGetAsync` en *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="01400-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="01400-219">Inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="01400-220">Para pasar parámetros a un mensaje de registro, defina hasta seis tipos al crear el campo estático.</span><span class="sxs-lookup"><span data-stu-id="01400-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="01400-221">La aplicación de ejemplo registra una cadena cuando se agrega una cita. Para ello, define un tipo `string` para el campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="01400-222">La plantilla de mensaje de registro del delegado recibe sus valores de marcador de posición a partir de los tipos proporcionados.</span><span class="sxs-lookup"><span data-stu-id="01400-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="01400-223">La aplicación de ejemplo define un delegado para agregar una cita cuando el parámetro de la cita es `string`:</span><span class="sxs-lookup"><span data-stu-id="01400-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="01400-224">El método de extensión estático para agregar una cita, `QuoteAdded`, recibe el valor de argumento de la cita y lo pasa al delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="01400-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="01400-225">En el modelo de página para la página de índice (*Pages/Index.cshtml.cs*), se llama a `QuoteAdded` para registrar el mensaje:</span><span class="sxs-lookup"><span data-stu-id="01400-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="01400-226">Inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="01400-227">La aplicación de ejemplo implementa un patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para la eliminación de la cita.</span><span class="sxs-lookup"><span data-stu-id="01400-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="01400-228">Se registra un mensaje informativo si se realiza correctamente una operación de eliminación.</span><span class="sxs-lookup"><span data-stu-id="01400-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="01400-229">Se registra un mensaje de error para una operación de eliminación si se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="01400-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="01400-230">El mensaje de registro de la operación de eliminación con error incluye el seguimiento de la pila de excepciones (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="01400-231">Observe cómo se pasa la excepción al delegado en `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="01400-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="01400-232">En el modelo de página para la página de índice, una operación correcta de eliminación de cita llama al método `QuoteDeleted` en el registrador.</span><span class="sxs-lookup"><span data-stu-id="01400-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="01400-233">Cuando no se encuentra una cita para su eliminación, se produce una excepción <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="01400-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="01400-234">La excepción se captura mediante la instrucción [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) y se registra mediante una llamada al método `QuoteDeleteFailed` en el registrador en el bloque [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="01400-235">Cuando se elimine correctamente una cita, inspeccione la salida de la consola de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01400-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="01400-236">Cuando se produzca un error en la eliminación de una cita, inspeccione la salida de la consola de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01400-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="01400-237">Tenga en cuenta que la excepción se incluye en el mensaje del registro:</span><span class="sxs-lookup"><span data-stu-id="01400-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="01400-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="01400-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="01400-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegado <xref:System.Func%601> para definir un [ámbito de registro](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="01400-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="01400-240">Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permiten pasar hasta tres parámetros de tipo a una cadena de formato con nombre (plantilla).</span><span class="sxs-lookup"><span data-stu-id="01400-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="01400-241">Al igual que sucede con el método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> es una plantilla y no una cadena interpolada.</span><span class="sxs-lookup"><span data-stu-id="01400-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="01400-242">Los marcadores de posición se rellenan en el orden en que se especifican los tipos.</span><span class="sxs-lookup"><span data-stu-id="01400-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="01400-243">Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas.</span><span class="sxs-lookup"><span data-stu-id="01400-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="01400-244">Sirven como nombres de propiedad en los datos estructurados del registro.</span><span class="sxs-lookup"><span data-stu-id="01400-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="01400-245">Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición.</span><span class="sxs-lookup"><span data-stu-id="01400-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="01400-246">Por ejemplo: `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="01400-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="01400-247">Defina un [ámbito de registro](xref:fundamentals/logging/index#log-scopes) para aplicarlo a una serie de mensajes de registro mediante el método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="01400-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="01400-248">La aplicación de ejemplo tiene un botón **Borrar todo** para eliminar todas las citas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="01400-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="01400-249">Para eliminar las citas, se van quitando de una en una.</span><span class="sxs-lookup"><span data-stu-id="01400-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="01400-250">Cada vez que se elimina una cita, se llama al método `QuoteDeleted` en el registrador.</span><span class="sxs-lookup"><span data-stu-id="01400-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="01400-251">Se agrega un ámbito de registro a estos mensajes de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="01400-252">Habilite `IncludeScopes` en la sección del registrador de consola de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="01400-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="01400-253">Para crear un ámbito de registro, agregue un campo para que contenga un delegado <xref:System.Func%601> para el ámbito.</span><span class="sxs-lookup"><span data-stu-id="01400-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="01400-254">La aplicación de ejemplo crea un campo denominado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="01400-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="01400-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para crear el delegado.</span><span class="sxs-lookup"><span data-stu-id="01400-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="01400-256">Pueden especificarse hasta tres tipos para usarlos como argumentos de plantilla cuando se invoca el delegado.</span><span class="sxs-lookup"><span data-stu-id="01400-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="01400-257">La aplicación de ejemplo usa una plantilla de mensaje que incluye el número de citas eliminadas (un tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="01400-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="01400-258">Proporcione un método de extensión estático para el mensaje de registro.</span><span class="sxs-lookup"><span data-stu-id="01400-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="01400-259">Incluya todos los parámetros de tipo para propiedades con nombre que aparezcan en la plantilla de mensaje.</span><span class="sxs-lookup"><span data-stu-id="01400-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="01400-260">La aplicación de ejemplo toma un valor de número `count` de citas que se van a eliminar y devuelve `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="01400-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="01400-261">El ámbito encapsula las llamadas a la extensión de registro en un bloque [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="01400-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="01400-262">Inspeccione los mensajes de registro en la salida de la consola de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01400-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="01400-263">En el resultado siguiente se muestran tres citas eliminadas con el mensaje del ámbito de registro incluido:</span><span class="sxs-lookup"><span data-stu-id="01400-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="01400-264">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="01400-264">Additional resources</span></span>

* [<span data-ttu-id="01400-265">Registro</span><span class="sxs-lookup"><span data-stu-id="01400-265">Logging</span></span>](xref:fundamentals/logging/index)
