---
title: Llamada a servicios gRPC con el cliente .NET
author: jamesnk
description: Obtenga información sobre cómo llamar a servicios gRPC con el cliente gRPC de .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 9ebe36cdb17e858fd82216b090e3e89169197101
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406191"
---
# <a name="call-grpc-services-with-the-net-client"></a>Llamada a servicios gRPC con el cliente .NET

Hay una biblioteca cliente gRPC de .NET disponible en el paquete NuGet [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client). En este documento se explica cómo:

* Configurar un cliente gRPC para llamar a servicios gRPC.
* Realizar llamadas gRPC a los métodos de streaming unario, de servidor, de cliente y bidireccional.

## <a name="configure-grpc-client"></a>Configuración del cliente gRPC

Los clientes gRPC son tipos de cliente concretos que se [generan a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets). El cliente gRPC concreto tiene métodos que se convierten en el servicio gRPC en el archivo *\*.proto*.

Un cliente gRPC se crea a partir de un canal. Para empezar, use `GrpcChannel.ForAddress` para crear un canal y, después, use el canal para crear un cliente gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Un canal representa una conexión de larga duración con un servicio gRPC. Cuando se crea un canal, se configura con opciones relacionadas con la llamada a un servicio. Por ejemplo, el elemento `HttpClient` que se usa para realizar llamadas, el tamaño máximo del mensaje de envío y recepción, y el registro se pueden especificar en `GrpcChannelOptions` y usar con `GrpcChannel.ForAddress`. Para obtener una lista completa de las opciones, vea [Opciones de configuración de cliente](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Rendimiento y uso de canales y clientes:

* La creación de un canal puede ser una operación costosa. La reutilización de un canal para las llamadas de gRPC proporciona ventajas de rendimiento.
* Los clientes gRPC se crean con canales. Los clientes gRPC son objetos ligeros y no es necesario que se almacenen en caché ni reutilizarlos.
* Se pueden crear varios clientes gRPC a partir de un canal, incluidos distintos tipos de clientes.
* Varios subprocesos pueden usar de forma segura un canal y los clientes creados a partir del canal.
* Los clientes creados a partir del canal pueden realizar varias llamadas simultáneas.

`GrpcChannel.ForAddress` no es la única opción para crear un cliente gRPC. Si llama a servicios gRPC desde una aplicación ASP.NET Core, considere la posibilidad de usar la [integración de fábrica de cliente de gRPC](xref:grpc/clientfactory). La integración de gRPC con `HttpClientFactory` ofrece una alternativa centralizada a la creación de clientes gRPC.

> [!NOTE]
> Se requiere configuración adicional para [llamar a servicios gRPC no seguros con el cliente de .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> La llamada a gRPC a través de HTTP/2 con `Grpc.Net.Client` no se admite actualmente en Xamarin. Estamos trabajando para mejorar la compatibilidad con HTTP/2 en una versión futura de Xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) y [gRPC-Web](xref:grpc/browser) son alternativas viables que funcionan en la actualidad.

## <a name="make-grpc-calls"></a>Realización de llamadas gRPC

Una llamada gRPC se inicia mediante la llamada a un método en el cliente. El cliente gRPC controlará la serialización de mensajes y dirigirá la llamada gRPC al servicio correcto.

gRPC tiene distintos tipos de métodos. La forma en que se utiliza el cliente para hacer una llamada gRPC depende del tipo de método llamado. Los tipos de métodos gRPC son los siguientes:

* Unario
* Streaming de servidor
* Streaming de cliente
* Streaming bidireccional

### <a name="unary-call"></a>Llamada unaria

Una llamada unaria comienza con el envío de un mensaje de solicitud por parte del cliente. Cuando finaliza el servicio, se devuelve un mensaje de respuesta.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Cada método de servicio unario del archivo *\*.proto* dará como resultado dos métodos de .NET en el tipo de cliente gRPC concreto para llamar al método: un método asincrónico y un método de bloqueo. Por ejemplo, en `GreeterClient` hay dos maneras de llamar a `SayHello`:

* `GreeterClient.SayHelloAsync`: llama al servicio `Greeter.SayHello` de forma asincrónica. Se le puede aplicar espera.
* `GreeterClient.SayHello`: llama al servicio `Greeter.SayHello` y se bloquea hasta que se completa. No se debe usar en código asincrónico.

### <a name="server-streaming-call"></a>Llamada de streaming de servidor

Una llamada de streaming de servidor comienza con el envío de un mensaje de solicitud por parte del cliente. `ResponseStream.MoveNext()` lee los mensajes transmitidos desde el servicio. La llamada de streaming del servidor se completa cuando `ResponseStream.MoveNext()` devuelve `false`.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Al usar C# 8 o una versión posterior, se puede usar la sintaxis de `await foreach` para leer los mensajes. El método de extensión `IAsyncStreamReader<T>.ReadAllAsync()` lee todos los mensajes de la secuencia de respuesta:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Llamada de streaming de cliente

Una llamada de streaming de cliente comienza *sin* el envío de un mensaje por parte del cliente. El cliente puede elegir enviar mensajes con `RequestStream.WriteAsync`. Cuando el cliente ha terminado de enviar mensajes, se debe llamar a `RequestStream.CompleteAsync` para notificar al servicio. La llamada finaliza cuando el servicio devuelve un mensaje de respuesta.

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a>Llamada de streaming bidireccional

Una llamada de streaming bidireccional comienza *sin* el envío de un mensaje por parte del cliente. El cliente puede elegir enviar mensajes con `RequestStream.WriteAsync`. Se puede acceder a los mensajes transmitidos desde el servicio con `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()`. La llamada de streaming bidireccional se completa cuando `ResponseStream` no tiene más mensajes.

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

Durante una llamada de streaming bidireccional, el cliente y el servicio se pueden enviar mensajes entre sí en cualquier momento. La mejor lógica de cliente para interactuar con una llamada bidireccional varía en función de la lógica del servicio.

## <a name="access-grpc-trailers"></a>Acceso a los finalizadores gRPC

Las llamadas gRPC pueden devolver finalizadores gRPC. Los finalizadores gRPC se usan para proporcionar metadatos de nombre y valor sobre una llamada. Los finalizadores proporcionan una funcionalidad similar a los encabezados HTTP, pero se reciben al final de la llamada.

Los finalizadores gRPC son accesibles mediante `GetTrailers()`, que devuelve una colección de metadatos. Los finalizadores se devuelven una vez que se completa la respuesta, por lo que debe esperar todos los mensajes de respuesta antes de acceder a ellos.

Las llamadas de streaming unarias y de cliente deben esperar `ResponseAsync` antes de llamar a `GetTrailers()`:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

Las llamadas de streaming bidireccionales y de servidor deben terminar de esperar la secuencia de respuesta antes de llamar a `GetTrailers()`:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

También se puede acceder a los finalizadores gRPC desde `RpcException`. Un servicio puede devolver los finalizadores junto con un estado de gRPC que no sea correcto. En esta situación, los finalizadores se recuperan de la excepción que genera el cliente de gRPC:

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
