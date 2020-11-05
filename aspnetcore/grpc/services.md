---
title: Creación de servicios y métodos gRPC
author: jamesnk
description: Obtenga información sobre cómo crear servicios y métodos gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058745"
---
# <a name="create-grpc-services-and-methods"></a>Creación de servicios y métodos gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

En este documento se explica cómo crear servicios y métodos gRPC en C#. Contenido de los temas:

* Procedimientos para definir servicios y métodos en archivos *.proto*.
* Código generado mediante herramientas de gRPC en C#.
* Implementación de servicios y métodos gRPC.

## <a name="create-new-grpc-services"></a>Creación de servicios gRPC nuevos

Los [servicios gRPC con C#](xref:grpc/basics) presentaron el enfoque "contract-first" de gRPC para el desarrollo de API, en el que primero se diseña el modo de comunicación entre los servicios y, después, los propios servicios. Los servicios y mensajes se definen en los archivos *.proto*. Posteriormente, las herramientas de C# generan código a partir de los archivos *.proto*. En el caso de los recursos del lado servidor, se genera un tipo base abstracto para cada servicio, junto con las clases para los mensajes.

El archivo *.proto* siguiente:

* Define un servicio `Greeter`.
* El servicio `Greeter` define una llamada a `SayHello`.
* `SayHello` envía un mensaje `HelloRequest` y recibe un mensaje `HelloReply`.

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Las herramientas de C# generan el tipo base `GreeterBase` de C#:

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

De forma predeterminada, el elemento `GreeterBase` generado no hace nada. Su método `SayHello` virtual devolverá un error `UNIMPLEMENTED` a cualquier cliente que llame. Para que el servicio sea útil, una aplicación debe crear una implementación concreta de `GreeterBase`:

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

La implementación del servicio se registra con la aplicación. Si el servicio lo hospeda gRPC de ASP.NET Core, debe agregarse a la canalización de enrutamiento con el método `MapGrpcService`.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Consulte <xref:grpc/aspnetcore> para obtener más información.

## <a name="implement-grpc-methods"></a>Implementación de métodos gRPC

Un servicio gRPC puede tener distintos tipos de métodos. El modo en el que un servicio envía y recibe los mensajes depende del tipo de método definido. Los tipos de métodos gRPC son los siguientes:

* Unario
* Streaming de servidor
* Streaming de cliente
* Streaming bidireccional

Las llamadas de streaming se especifican con la palabra clave `stream` en el archivo *.proto*. `stream` se puede colocar en el mensaje de solicitud de una llamada, en el mensaje de respuesta o en ambos.

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

Cada tipo de llamada tiene una signatura de método distinta. El reemplazo de métodos generados a partir del tipo de servicio base abstracto en una implementación concreta garantiza que se usan los argumentos correctos y el tipo de valor devuelto.

### <a name="unary-method"></a>Método unario

Un método unario obtiene el mensaje de solicitud como parámetro y devuelve la respuesta. Se completa una llamada unaria cuando se devuelve la respuesta.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Las llamadas unarias son las más parecidas a las [acciones en los controladores de API web](xref:web-api/index). Una diferencia importante de los métodos gRPC respecto a las acciones es que los métodos gRPC no pueden enlazar partes de una solicitud a argumentos de método distintos. Los métodos gRPC siempre tienen un argumento de mensaje para los datos de la solicitud entrante. Todavía se pueden enviar varios valores a un servicio gRPC haciendo que sean campos en el mensaje de solicitud:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Método de streaming de servidor

Un método de streaming de servidor obtiene el mensaje de solicitud como parámetro. Dado que se pueden transmitir varios mensajes de vuelta al autor de la llamada, `responseStream.WriteAsync` se usa para enviar mensajes de respuesta. Una llamada de streaming de servidor se completa cuando el método devuelve.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

El cliente no tiene ninguna manera de enviar mensajes o datos adicionales una vez que se ha iniciado el método de streaming de servidor. Algunos métodos de streaming están diseñados para ejecutarse indefinidamente. En el caso de los métodos de streaming continuos, un cliente puede cancelar la llamada cuando ya no se necesite. Cuando se produce la cancelación, el cliente envía una señal al servidor y se genera [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken). El token `CancellationToken` debe usarse en el servidor con métodos asincrónicos para que ocurra lo siguiente:

* Cualquier trabajo asincrónico se cancela junto con la llamada de streaming.
* El método finaliza rápidamente.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a>Método de streaming de cliente

Un método de streaming de cliente comienza *sin* que el método reciba un mensaje. El parámetro `requestStream` se utiliza para leer los mensajes del cliente. Cuando se devuelve un mensaje de respuesta, se completa una llamada de streaming de cliente:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

Al usar C# 8 o una versión posterior, se puede usar la sintaxis de `await foreach` para leer los mensajes. El método de extensión `IAsyncStreamReader<T>.ReadAllAsync()` lee todos los mensajes del flujo de la solicitud:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a>Método de streaming bidireccional

Un método de streaming bidireccional comienza *sin* que el método reciba un mensaje. El parámetro `requestStream` se utiliza para leer los mensajes del cliente. El método	puede elegir enviar mensajes con `responseStream.WriteAsync`. Una llamada de streaming bidireccional se completa cuando el método devuelve lo siguiente:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

El código anterior:

* Envía una respuesta para cada solicitud.
* Es un uso básico del streaming bidireccional.

Es posible admitir escenarios más complejos, como la lectura de solicitudes y el envío de respuestas de forma simultánea:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

En un método de streaming bidireccional, el cliente y el servicio se pueden enviar mensajes entre sí en cualquier momento. La mejor implementación de un método bidireccional varía en función de los requisitos.

## <a name="access-grpc-request-headers"></a>Acceso a los encabezados de solicitud gRPC

Un mensaje de solicitud no es la única manera que tiene un cliente para enviar datos a un servicio gRPC. Los valores de encabezado están disponibles en un servicio mediante `ServerCallContext.RequestHeaders`.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/basics>
* <xref:grpc/client>
