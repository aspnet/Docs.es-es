---
title: Registro de alto rendimiento con LoggerMessage en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar LoggerMessage para crear delegados almacenables en caché que requieren menos asignaciones de objetos que los escenarios de registro de alto rendimiento.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: dc90c6cdbcea7c36079bcb4a032014998056b56d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401173"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Registro de alto rendimiento con LoggerMessage en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> crean delegados almacenables en caché que requieren menos asignaciones de objetos y una menor sobrecarga computacional en comparación con los [métodos de extensión del registrador](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Para escenarios de registro de alto rendimiento, use el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.

<xref:Microsoft.Extensions.Logging.LoggerMessage> proporciona las siguientes ventajas de rendimiento frente a los métodos de extensión del registrador:

* Los métodos de extensión del registrador requieren la conversión boxing de tipos de valor, como `int`, en `object`. El patrón <xref:Microsoft.Extensions.Logging.LoggerMessage> impide la conversión boxing mediante métodos de extensión y campos <xref:System.Action> estáticos con parámetros fuertemente tipados.
* Los métodos de extensión del registrador deben analizar la plantilla de mensaje (cadena de formato con nombre) cada vez que se escribe un mensaje de registro. <xref:Microsoft.Extensions.Logging.LoggerMessage> solo necesita analizar una vez una plantilla cuando se define el mensaje.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

La aplicación de ejemplo muestra las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema de seguimiento de citas básico. La aplicación agrega y elimina citas mediante una base de datos en memoria. A medida que se producen estas operaciones, se generan mensajes de registro mediante el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegado <xref:System.Action> para registrar un mensaje. Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permiten pasar hasta seis parámetros de tipo a una cadena de formato con nombre (plantilla).

La cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> es una plantilla y no una cadena interpolada. Los marcadores de posición se rellenan en el orden en que se especifican los tipos. Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas. Sirven como nombres de propiedad en los datos estructurados del registro. Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición. Por ejemplo: `{Count}`, `{FirstName}`.

Cada mensaje de registro es un delegado <xref:System.Action> que se mantiene en un campo estático creado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Por ejemplo, la aplicación de ejemplo crea un campo que describe un mensaje de registro para una solicitud GET para la página de índice (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Especifique lo siguiente para el delegado <xref:System.Action>:

* El nivel de registro.
* Un identificador de evento único (<xref:Microsoft.Extensions.Logging.EventId>) con el nombre del método de extensión estático.
* La plantilla de mensaje (cadena de formato con nombre). 

Una solicitud para la página de índice de la aplicación de ejemplo establece:

* El nivel de registro en `Information`.
* El identificador de evento en `1` con el nombre del método `IndexPageRequested`.
* La plantilla de mensaje (cadena de formato con nombre) en una cadena.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Los almacenes de registro estructurado pueden usar el nombre de evento cuando se suministra con el identificador de evento para enriquecer el registro. Por ejemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa el nombre de evento.

El delegado <xref:System.Action> se invoca mediante un método de extensión fuertemente tipado. El método `IndexPageRequested` registra un mensaje para una solicitud GET de página de índice en la aplicación de ejemplo:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

Se llama a `IndexPageRequested` en el registrador en el método `OnGetAsync` en *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Para pasar parámetros a un mensaje de registro, defina hasta seis tipos al crear el campo estático. La aplicación de ejemplo registra una cadena cuando se agrega una cita. Para ello, define un tipo `string` para el campo <xref:System.Action>:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

La plantilla de mensaje de registro del delegado recibe sus valores de marcador de posición a partir de los tipos proporcionados. La aplicación de ejemplo define un delegado para agregar una cita cuando el parámetro de la cita es `string`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

El método de extensión estático para agregar una cita, `QuoteAdded`, recibe el valor de argumento de la cita y lo pasa al delegado <xref:System.Action>:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

En el modelo de página para la página de índice (*Pages/Index.cshtml.cs*), se llama a `QuoteAdded` para registrar el mensaje:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

La aplicación de ejemplo implementa un patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para la eliminación de la cita. Se registra un mensaje informativo si se realiza correctamente una operación de eliminación. Se registra un mensaje de error para una operación de eliminación si se produce una excepción. El mensaje de registro de la operación de eliminación con error incluye el seguimiento de la pila de excepciones (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Observe cómo se pasa la excepción al delegado en `QuoteDeleteFailed`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

En el modelo de página para la página de índice, una operación correcta de eliminación de cita llama al método `QuoteDeleted` en el registrador. Cuando no se encuentra una cita para su eliminación, se produce una excepción <xref:System.ArgumentNullException>. La excepción se captura mediante la instrucción [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) y se registra mediante una llamada al método `QuoteDeleteFailed` en el registrador en el bloque [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Cuando se elimine correctamente una cita, inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Cuando se produzca un error en la eliminación de una cita, inspeccione la salida de la consola de la aplicación. Tenga en cuenta que la excepción se incluye en el mensaje del registro:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegado <xref:System.Func%601> para definir un [ámbito de registro](xref:fundamentals/logging/index#log-scopes). Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permiten pasar hasta tres parámetros de tipo a una cadena de formato con nombre (plantilla).

Al igual que sucede con el método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> es una plantilla y no una cadena interpolada. Los marcadores de posición se rellenan en el orden en que se especifican los tipos. Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas. Sirven como nombres de propiedad en los datos estructurados del registro. Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición. Por ejemplo: `{Count}`, `{FirstName}`.

Defina un [ámbito de registro](xref:fundamentals/logging/index#log-scopes) para aplicarlo a una serie de mensajes de registro mediante el método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.

La aplicación de ejemplo tiene un botón **Borrar todo** para eliminar todas las citas de la base de datos. Para eliminar las citas, se van quitando de una en una. Cada vez que se elimina una cita, se llama al método `QuoteDeleted` en el registrador. Se agrega un ámbito de registro a estos mensajes de registro.

Habilite `IncludeScopes` en la sección del registrador de la consola de *appsettings.json*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Para crear un ámbito de registro, agregue un campo para que contenga un delegado <xref:System.Func%601> para el ámbito. La aplicación de ejemplo crea un campo denominado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para crear el delegado. Pueden especificarse hasta tres tipos para usarlos como argumentos de plantilla cuando se invoca el delegado. La aplicación de ejemplo usa una plantilla de mensaje que incluye el número de citas eliminadas (un tipo `int`):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Proporcione un método de extensión estático para el mensaje de registro. Incluya todos los parámetros de tipo para propiedades con nombre que aparezcan en la plantilla de mensaje. La aplicación de ejemplo toma un valor de número `count` de citas que se van a eliminar y devuelve `_allQuotesDeletedScope`:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

El ámbito encapsula las llamadas a la extensión de registro en un bloque [using](/dotnet/csharp/language-reference/keywords/using-statement):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Inspeccione los mensajes de registro en la salida de la consola de la aplicación. En el resultado siguiente se muestran tres citas eliminadas con el mensaje del ámbito de registro incluido:

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

Las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> crean delegados almacenables en caché que requieren menos asignaciones de objetos y una menor sobrecarga computacional en comparación con los [métodos de extensión del registrador](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Para escenarios de registro de alto rendimiento, use el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.

<xref:Microsoft.Extensions.Logging.LoggerMessage> proporciona las siguientes ventajas de rendimiento frente a los métodos de extensión del registrador:

* Los métodos de extensión del registrador requieren la conversión boxing de tipos de valor, como `int`, en `object`. El patrón <xref:Microsoft.Extensions.Logging.LoggerMessage> impide la conversión boxing mediante métodos de extensión y campos <xref:System.Action> estáticos con parámetros fuertemente tipados.
* Los métodos de extensión del registrador deben analizar la plantilla de mensaje (cadena de formato con nombre) cada vez que se escribe un mensaje de registro. <xref:Microsoft.Extensions.Logging.LoggerMessage> solo necesita analizar una vez una plantilla cuando se define el mensaje.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

La aplicación de ejemplo muestra las características de <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema de seguimiento de citas básico. La aplicación agrega y elimina citas mediante una base de datos en memoria. A medida que se producen estas operaciones, se generan mensajes de registro mediante el patrón <xref:Microsoft.Extensions.Logging.LoggerMessage>.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegado <xref:System.Action> para registrar un mensaje. Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permiten pasar hasta seis parámetros de tipo a una cadena de formato con nombre (plantilla).

La cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> es una plantilla y no una cadena interpolada. Los marcadores de posición se rellenan en el orden en que se especifican los tipos. Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas. Sirven como nombres de propiedad en los datos estructurados del registro. Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición. Por ejemplo: `{Count}`, `{FirstName}`.

Cada mensaje de registro es un delegado <xref:System.Action> que se mantiene en un campo estático creado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Por ejemplo, la aplicación de ejemplo crea un campo que describe un mensaje de registro para una solicitud GET para la página de índice (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Especifique lo siguiente para el delegado <xref:System.Action>:

* El nivel de registro.
* Un identificador de evento único (<xref:Microsoft.Extensions.Logging.EventId>) con el nombre del método de extensión estático.
* La plantilla de mensaje (cadena de formato con nombre). 

Una solicitud para la página de índice de la aplicación de ejemplo establece:

* El nivel de registro en `Information`.
* El identificador de evento en `1` con el nombre del método `IndexPageRequested`.
* La plantilla de mensaje (cadena de formato con nombre) en una cadena.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Los almacenes de registro estructurado pueden usar el nombre de evento cuando se suministra con el identificador de evento para enriquecer el registro. Por ejemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa el nombre de evento.

El delegado <xref:System.Action> se invoca mediante un método de extensión fuertemente tipado. El método `IndexPageRequested` registra un mensaje para una solicitud GET de página de índice en la aplicación de ejemplo:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

Se llama a `IndexPageRequested` en el registrador en el método `OnGetAsync` en *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Para pasar parámetros a un mensaje de registro, defina hasta seis tipos al crear el campo estático. La aplicación de ejemplo registra una cadena cuando se agrega una cita. Para ello, define un tipo `string` para el campo <xref:System.Action>:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

La plantilla de mensaje de registro del delegado recibe sus valores de marcador de posición a partir de los tipos proporcionados. La aplicación de ejemplo define un delegado para agregar una cita cuando el parámetro de la cita es `string`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

El método de extensión estático para agregar una cita, `QuoteAdded`, recibe el valor de argumento de la cita y lo pasa al delegado <xref:System.Action>:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

En el modelo de página para la página de índice (*Pages/Index.cshtml.cs*), se llama a `QuoteAdded` para registrar el mensaje:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

La aplicación de ejemplo implementa un patrón [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para la eliminación de la cita. Se registra un mensaje informativo si se realiza correctamente una operación de eliminación. Se registra un mensaje de error para una operación de eliminación si se produce una excepción. El mensaje de registro de la operación de eliminación con error incluye el seguimiento de la pila de excepciones (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Observe cómo se pasa la excepción al delegado en `QuoteDeleteFailed`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

En el modelo de página para la página de índice, una operación correcta de eliminación de cita llama al método `QuoteDeleted` en el registrador. Cuando no se encuentra una cita para su eliminación, se produce una excepción <xref:System.ArgumentNullException>. La excepción se captura mediante la instrucción [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) y se registra mediante una llamada al método `QuoteDeleteFailed` en el registrador en el bloque [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Cuando se elimine correctamente una cita, inspeccione la salida de la consola de la aplicación:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Cuando se produzca un error en la eliminación de una cita, inspeccione la salida de la consola de la aplicación. Tenga en cuenta que la excepción se incluye en el mensaje del registro:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegado <xref:System.Func%601> para definir un [ámbito de registro](xref:fundamentals/logging/index#log-scopes). Las sobrecargas <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permiten pasar hasta tres parámetros de tipo a una cadena de formato con nombre (plantilla).

Al igual que sucede con el método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la cadena proporcionada al método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> es una plantilla y no una cadena interpolada. Los marcadores de posición se rellenan en el orden en que se especifican los tipos. Los nombres de los marcadores de posición en la plantilla deben ser descriptivos y coherentes entre las plantillas. Sirven como nombres de propiedad en los datos estructurados del registro. Se recomienda el uso de la [grafía Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) para los nombres de los marcadores de posición. Por ejemplo: `{Count}`, `{FirstName}`.

Defina un [ámbito de registro](xref:fundamentals/logging/index#log-scopes) para aplicarlo a una serie de mensajes de registro mediante el método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.

La aplicación de ejemplo tiene un botón **Borrar todo** para eliminar todas las citas de la base de datos. Para eliminar las citas, se van quitando de una en una. Cada vez que se elimina una cita, se llama al método `QuoteDeleted` en el registrador. Se agrega un ámbito de registro a estos mensajes de registro.

Habilite `IncludeScopes` en la sección del registrador de la consola de *appsettings.json*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Para crear un ámbito de registro, agregue un campo para que contenga un delegado <xref:System.Func%601> para el ámbito. La aplicación de ejemplo crea un campo denominado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para crear el delegado. Pueden especificarse hasta tres tipos para usarlos como argumentos de plantilla cuando se invoca el delegado. La aplicación de ejemplo usa una plantilla de mensaje que incluye el número de citas eliminadas (un tipo `int`):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Proporcione un método de extensión estático para el mensaje de registro. Incluya todos los parámetros de tipo para propiedades con nombre que aparezcan en la plantilla de mensaje. La aplicación de ejemplo toma un valor de número `count` de citas que se van a eliminar y devuelve `_allQuotesDeletedScope`:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

El ámbito encapsula las llamadas a la extensión de registro en un bloque [using](/dotnet/csharp/language-reference/keywords/using-statement):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Inspeccione los mensajes de registro en la salida de la consola de la aplicación. En el resultado siguiente se muestran tres citas eliminadas con el mensaje del ámbito de registro incluido:

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

## <a name="additional-resources"></a>Recursos adicionales

* [Registro](xref:fundamentals/logging/index)
