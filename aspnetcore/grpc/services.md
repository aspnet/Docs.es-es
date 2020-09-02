---
title: Creación de servicios y métodos gRPC
author: jamesnk
description: Obtenga información sobre cómo crear servicios y métodos gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
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
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945578"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="7f0a2-103">Creación de servicios y métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="7f0a2-103">Create gRPC services and methods</span></span>

<span data-ttu-id="7f0a2-104">En este documento se explica cómo crear servicios y métodos gRPC en C#.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-104">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="7f0a2-105">Contenido de los temas:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-105">Topics include:</span></span>

* <span data-ttu-id="7f0a2-106">Procedimientos para definir servicios y métodos en archivos *.proto*.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-106">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="7f0a2-107">Código generado mediante herramientas de gRPC en C#.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-107">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="7f0a2-108">Implementación de servicios y métodos gRPC.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-108">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="7f0a2-109">Creación de servicios gRPC nuevos</span><span class="sxs-lookup"><span data-stu-id="7f0a2-109">Create new gRPC services</span></span>

<span data-ttu-id="7f0a2-110">Los [servicios gRPC con C#](xref:grpc/basics) presentaron el enfoque "contract-first" de gRPC para el desarrollo de API, en el que primero se diseña el modo de comunicación entre los servicios y, después, los propios servicios.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-110">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="7f0a2-111">Los servicios y mensajes se definen en los archivos *.proto*.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-111">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="7f0a2-112">Posteriormente, las herramientas de C# generan código a partir de los archivos *.proto*.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-112">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="7f0a2-113">En el caso de los recursos del lado servidor, se genera un tipo base abstracto para cada servicio, junto con las clases para los mensajes.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-113">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="7f0a2-114">El archivo *.proto* siguiente:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-114">The following *.proto* file:</span></span>

* <span data-ttu-id="7f0a2-115">Define un servicio `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-115">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="7f0a2-116">El servicio `Greeter` define una llamada a `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-116">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="7f0a2-117">`SayHello` envía un mensaje `HelloRequest` y recibe un mensaje `HelloReply`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-117">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="7f0a2-118">Las herramientas de C# generan el tipo base `GreeterBase` de C#:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-118">C# tooling generates the C# `GreeterBase` base type:</span></span>

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

<span data-ttu-id="7f0a2-119">De forma predeterminada, el elemento `GreeterBase` generado no hace nada.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-119">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="7f0a2-120">Su método `SayHello` virtual devolverá un error `UNIMPLEMENTED` a cualquier cliente que llame.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-120">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="7f0a2-121">Para que el servicio sea útil, una aplicación debe crear una implementación concreta de `GreeterBase`:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-121">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="7f0a2-122">La implementación del servicio se registra con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-122">The service implementation is registered with the app.</span></span> <span data-ttu-id="7f0a2-123">Si el servicio lo hospeda gRPC de ASP.NET Core, debe agregarse a la canalización de enrutamiento con el método `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-123">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="7f0a2-124">Consulte <xref:grpc/aspnetcore> para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-124">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="7f0a2-125">Implementación de métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="7f0a2-125">Implement gRPC methods</span></span>

<span data-ttu-id="7f0a2-126">Un servicio gRPC puede tener distintos tipos de métodos.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-126">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="7f0a2-127">El modo en el que un servicio envía y recibe los mensajes depende del tipo de método definido.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-127">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="7f0a2-128">Los tipos de métodos gRPC son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-128">The gRPC method types are:</span></span>

* <span data-ttu-id="7f0a2-129">Unario</span><span class="sxs-lookup"><span data-stu-id="7f0a2-129">Unary</span></span>
* <span data-ttu-id="7f0a2-130">Streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="7f0a2-130">Server streaming</span></span>
* <span data-ttu-id="7f0a2-131">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="7f0a2-131">Client streaming</span></span>
* <span data-ttu-id="7f0a2-132">Streaming bidireccional</span><span class="sxs-lookup"><span data-stu-id="7f0a2-132">Bi-directional streaming</span></span>

<span data-ttu-id="7f0a2-133">Las llamadas de streaming se especifican con la palabra clave `stream` en el archivo *.proto*.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-133">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="7f0a2-134">`stream` se puede colocar en el mensaje de solicitud de una llamada, en el mensaje de respuesta o en ambos.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-134">`stream` can be placed on a call's request message, response message, or both.</span></span>

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

<span data-ttu-id="7f0a2-135">Cada tipo de llamada tiene una signatura de método distinta.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-135">Each call type has a different method signature.</span></span> <span data-ttu-id="7f0a2-136">El reemplazo de métodos generados a partir del tipo de servicio base abstracto en una implementación concreta garantiza que se usan los argumentos correctos y el tipo de valor devuelto.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-136">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="7f0a2-137">Método unario</span><span class="sxs-lookup"><span data-stu-id="7f0a2-137">Unary method</span></span>

<span data-ttu-id="7f0a2-138">Un método unario obtiene el mensaje de solicitud como parámetro y devuelve la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-138">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="7f0a2-139">Se completa una llamada unaria cuando se devuelve la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-139">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="7f0a2-140">Las llamadas unarias son las más parecidas a las [acciones en los controladores de API web](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="7f0a2-140">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="7f0a2-141">Una diferencia importante de los métodos gRPC respecto a las acciones es que los métodos gRPC no pueden enlazar partes de una solicitud a argumentos de método distintos.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-141">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="7f0a2-142">Los métodos gRPC siempre tienen un argumento de mensaje para los datos de la solicitud entrante.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-142">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="7f0a2-143">Todavía se pueden enviar varios valores a un servicio gRPC haciendo que sean campos en el mensaje de solicitud:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-143">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="7f0a2-144">Método de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="7f0a2-144">Server streaming method</span></span>

<span data-ttu-id="7f0a2-145">Un método de streaming de servidor obtiene el mensaje de solicitud como parámetro.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-145">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="7f0a2-146">Dado que se pueden transmitir varios mensajes de vuelta al autor de la llamada, `responseStream.WriteAsync` se usa para enviar mensajes de respuesta.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-146">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="7f0a2-147">Una llamada de streaming de servidor se completa cuando el método devuelve.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-147">A server streaming call is complete when the method returns.</span></span>

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

<span data-ttu-id="7f0a2-148">El cliente no tiene ninguna manera de enviar mensajes o datos adicionales una vez que se ha iniciado el método de streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-148">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="7f0a2-149">Algunos métodos de streaming están diseñados para ejecutarse indefinidamente.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-149">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="7f0a2-150">En el caso de los métodos de streaming continuos, un cliente puede cancelar la llamada cuando ya no se necesite.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-150">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="7f0a2-151">Cuando se produce la cancelación, el cliente envía una señal al servidor y se genera [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken).</span><span class="sxs-lookup"><span data-stu-id="7f0a2-151">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="7f0a2-152">El token `CancellationToken` debe usarse en el servidor con métodos asincrónicos para que ocurra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-152">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="7f0a2-153">Cualquier trabajo asincrónico se cancela junto con la llamada de streaming.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-153">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="7f0a2-154">El método finaliza rápidamente.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-154">The method exits quickly.</span></span>

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

### <a name="client-streaming-method"></a><span data-ttu-id="7f0a2-155">Método de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="7f0a2-155">Client streaming method</span></span>

<span data-ttu-id="7f0a2-156">Un método de streaming de cliente comienza *sin* que el método reciba un mensaje.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-156">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7f0a2-157">El parámetro `requestStream` se utiliza para leer los mensajes del cliente.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-157">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7f0a2-158">Cuando se devuelve un mensaje de respuesta, se completa una llamada de streaming de cliente:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-158">A client streaming call is complete when a response message is returned:</span></span>

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

<span data-ttu-id="7f0a2-159">Al usar C# 8 o una versión posterior, se puede usar la sintaxis de `await foreach` para leer los mensajes.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-159">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="7f0a2-160">El método de extensión `IAsyncStreamReader<T>.ReadAllAsync()` lee todos los mensajes del flujo de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-160">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

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

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="7f0a2-161">Método de streaming bidireccional</span><span class="sxs-lookup"><span data-stu-id="7f0a2-161">Bi-directional streaming method</span></span>

<span data-ttu-id="7f0a2-162">Un método de streaming bidireccional comienza *sin* que el método reciba un mensaje.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-162">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7f0a2-163">El parámetro `requestStream` se utiliza para leer los mensajes del cliente.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-163">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7f0a2-164">El método	puede elegir enviar mensajes con `responseStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-164">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="7f0a2-165">Una llamada de streaming bidireccional se completa cuando el método devuelve lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-165">A bi-directional streaming call is complete when the the method returns:</span></span>

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

<span data-ttu-id="7f0a2-166">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-166">The preceding code:</span></span>

* <span data-ttu-id="7f0a2-167">Envía una respuesta para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-167">Sends a response for each request.</span></span>
* <span data-ttu-id="7f0a2-168">Es un uso básico del streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-168">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="7f0a2-169">Es posible admitir escenarios más complejos, como la lectura de solicitudes y el envío de respuestas de forma simultánea:</span><span class="sxs-lookup"><span data-stu-id="7f0a2-169">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

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

<span data-ttu-id="7f0a2-170">En un método de streaming bidireccional, el cliente y el servicio se pueden enviar mensajes entre sí en cualquier momento.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-170">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="7f0a2-171">La mejor implementación de un método bidireccional varía en función de los requisitos.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-171">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="7f0a2-172">Acceso a los encabezados de solicitud gRPC</span><span class="sxs-lookup"><span data-stu-id="7f0a2-172">Access gRPC request headers</span></span>

<span data-ttu-id="7f0a2-173">Un mensaje de solicitud no es la única manera que tiene un cliente para enviar datos a un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-173">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="7f0a2-174">Los valores de encabezado están disponibles en un servicio mediante `ServerCallContext.RequestHeaders`.</span><span class="sxs-lookup"><span data-stu-id="7f0a2-174">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="7f0a2-175">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7f0a2-175">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
