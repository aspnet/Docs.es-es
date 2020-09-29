---
title: Llamada a servicios gRPC con el cliente .NET
author: jamesnk
description: Obtenga información sobre cómo llamar a servicios gRPC con el cliente gRPC de .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 6515e87845cc5aa101532c18711d175a73581bee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722714"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="78273-103">Llamada a servicios gRPC con el cliente .NET</span><span class="sxs-lookup"><span data-stu-id="78273-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="78273-104">Hay una biblioteca cliente gRPC de .NET disponible en el paquete NuGet [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client).</span><span class="sxs-lookup"><span data-stu-id="78273-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="78273-105">En este documento se explica cómo:</span><span class="sxs-lookup"><span data-stu-id="78273-105">This document explains how to:</span></span>

* <span data-ttu-id="78273-106">Configurar un cliente gRPC para llamar a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="78273-107">Realizar llamadas gRPC a los métodos de streaming unario, de servidor, de cliente y bidireccional.</span><span class="sxs-lookup"><span data-stu-id="78273-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="78273-108">Configuración del cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="78273-108">Configure gRPC client</span></span>

<span data-ttu-id="78273-109">Los clientes gRPC son tipos de cliente concretos que se [generan a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="78273-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="78273-110">El cliente gRPC concreto tiene métodos que se convierten en el servicio gRPC en el archivo *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="78273-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="78273-111">Por ejemplo, un servicio denominado `Greeter` genera un tipo `GreeterClient` con métodos para llamar al servicio.</span><span class="sxs-lookup"><span data-stu-id="78273-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="78273-112">Un cliente gRPC se crea a partir de un canal.</span><span class="sxs-lookup"><span data-stu-id="78273-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="78273-113">Para empezar, use `GrpcChannel.ForAddress` para crear un canal y, después, use el canal para crear un cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="78273-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="78273-114">Un canal representa una conexión de larga duración con un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="78273-115">Cuando se crea un canal, se configura con opciones relacionadas con la llamada a un servicio.</span><span class="sxs-lookup"><span data-stu-id="78273-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="78273-116">Por ejemplo, el elemento `HttpClient` que se usa para realizar llamadas, el tamaño máximo del mensaje de envío y recepción, y el registro se pueden especificar en `GrpcChannelOptions` y usar con `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="78273-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="78273-117">Para obtener una lista completa de las opciones, vea [Opciones de configuración de cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="78273-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="78273-118">Configuración de TLS</span><span class="sxs-lookup"><span data-stu-id="78273-118">Configure TLS</span></span>

<span data-ttu-id="78273-119">El cliente de gRPC debe usar la misma seguridad de nivel de conexión que el servicio al que se ha llamado.</span><span class="sxs-lookup"><span data-stu-id="78273-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="78273-120">La seguridad de la capa de transporte (TLS) del cliente de gRPC se configura cuando se crea el canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="78273-121">El cliente de gRPC produce un error cuando llama a un servicio y la seguridad de nivel de conexión del canal y servicio no coinciden.</span><span class="sxs-lookup"><span data-stu-id="78273-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="78273-122">Para configurar un canal de gRPC para que use TLS, asegúrese de que la dirección del servidor comience por `https`.</span><span class="sxs-lookup"><span data-stu-id="78273-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="78273-123">Por ejemplo, `GrpcChannel.ForAddress("https://localhost:5001")` usa el protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78273-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="78273-124">El canal gRPC negocia automáticamente una conexión protegida por TLS y usa una conexión segura para realizar llamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="78273-125">gRPC admite la autenticación de certificados de cliente a través de TLS.</span><span class="sxs-lookup"><span data-stu-id="78273-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="78273-126">Para más información sobre la configuración de certificados de cliente con un canal gRPC, consulte <xref:grpc/authn-and-authz#client-certificate-authentication>.</span><span class="sxs-lookup"><span data-stu-id="78273-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="78273-127">Para llamar a servicios de gRPC no seguros, asegúrese de que la dirección del servidor comience por `http`.</span><span class="sxs-lookup"><span data-stu-id="78273-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="78273-128">Por ejemplo, `GrpcChannel.ForAddress("http://localhost:5000")` usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="78273-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="78273-129">En .NET Core 3.1 requiere configuración adicional para [llamar a servicios gRPC no seguros con el cliente de .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="78273-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="78273-130">Rendimiento del cliente</span><span class="sxs-lookup"><span data-stu-id="78273-130">Client performance</span></span>

<span data-ttu-id="78273-131">Rendimiento y uso de canales y clientes:</span><span class="sxs-lookup"><span data-stu-id="78273-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="78273-132">La creación de un canal puede ser una operación costosa.</span><span class="sxs-lookup"><span data-stu-id="78273-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="78273-133">La reutilización de un canal para las llamadas de gRPC proporciona ventajas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="78273-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="78273-134">Los clientes gRPC se crean con canales.</span><span class="sxs-lookup"><span data-stu-id="78273-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="78273-135">Los clientes gRPC son objetos ligeros y no es necesario que se almacenen en caché ni reutilizarlos.</span><span class="sxs-lookup"><span data-stu-id="78273-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="78273-136">Se pueden crear varios clientes gRPC a partir de un canal, incluidos distintos tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="78273-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="78273-137">Varios subprocesos pueden usar de forma segura un canal y los clientes creados a partir del canal.</span><span class="sxs-lookup"><span data-stu-id="78273-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="78273-138">Los clientes creados a partir del canal pueden realizar varias llamadas simultáneas.</span><span class="sxs-lookup"><span data-stu-id="78273-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="78273-139">`GrpcChannel.ForAddress` no es la única opción para crear un cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="78273-140">Si llama a servicios gRPC desde una aplicación ASP.NET Core, considere la posibilidad de usar la [integración de fábrica de cliente de gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="78273-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="78273-141">La integración de gRPC con `HttpClientFactory` ofrece una alternativa centralizada a la creación de clientes gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="78273-142">La llamada a gRPC a través de HTTP/2 con `Grpc.Net.Client` no se admite actualmente en Xamarin.</span><span class="sxs-lookup"><span data-stu-id="78273-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="78273-143">Estamos trabajando para mejorar la compatibilidad con HTTP/2 en una versión futura de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="78273-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="78273-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) y [gRPC-Web](xref:grpc/browser) son alternativas viables que funcionan en la actualidad.</span><span class="sxs-lookup"><span data-stu-id="78273-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="78273-145">Realización de llamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="78273-145">Make gRPC calls</span></span>

<span data-ttu-id="78273-146">Una llamada gRPC se inicia mediante la llamada a un método en el cliente.</span><span class="sxs-lookup"><span data-stu-id="78273-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="78273-147">El cliente gRPC controlará la serialización de mensajes y dirigirá la llamada gRPC al servicio correcto.</span><span class="sxs-lookup"><span data-stu-id="78273-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="78273-148">gRPC tiene distintos tipos de métodos.</span><span class="sxs-lookup"><span data-stu-id="78273-148">gRPC has different types of methods.</span></span> <span data-ttu-id="78273-149">La forma en que se utiliza el cliente para hacer una llamada gRPC depende del tipo de método llamado.</span><span class="sxs-lookup"><span data-stu-id="78273-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="78273-150">Los tipos de métodos gRPC son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="78273-150">The gRPC method types are:</span></span>

* <span data-ttu-id="78273-151">Unario</span><span class="sxs-lookup"><span data-stu-id="78273-151">Unary</span></span>
* <span data-ttu-id="78273-152">Streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="78273-152">Server streaming</span></span>
* <span data-ttu-id="78273-153">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="78273-153">Client streaming</span></span>
* <span data-ttu-id="78273-154">Streaming bidireccional</span><span class="sxs-lookup"><span data-stu-id="78273-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="78273-155">Llamada unaria</span><span class="sxs-lookup"><span data-stu-id="78273-155">Unary call</span></span>

<span data-ttu-id="78273-156">Una llamada unaria comienza con el envío de un mensaje de solicitud por parte del cliente.</span><span class="sxs-lookup"><span data-stu-id="78273-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="78273-157">Cuando finaliza el servicio, se devuelve un mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="78273-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="78273-158">Cada método de servicio unario del archivo *\*.proto* dará como resultado dos métodos de .NET en el tipo de cliente gRPC concreto para llamar al método: un método asincrónico y un método de bloqueo.</span><span class="sxs-lookup"><span data-stu-id="78273-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="78273-159">Por ejemplo, en `GreeterClient` hay dos maneras de llamar a `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="78273-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="78273-160">`GreeterClient.SayHelloAsync`: llama al servicio `Greeter.SayHello` de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="78273-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="78273-161">Se le puede aplicar espera.</span><span class="sxs-lookup"><span data-stu-id="78273-161">Can be awaited.</span></span>
* <span data-ttu-id="78273-162">`GreeterClient.SayHello`: llama al servicio `Greeter.SayHello` y se bloquea hasta que se completa.</span><span class="sxs-lookup"><span data-stu-id="78273-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="78273-163">No se debe usar en código asincrónico.</span><span class="sxs-lookup"><span data-stu-id="78273-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="78273-164">Llamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="78273-164">Server streaming call</span></span>

<span data-ttu-id="78273-165">Una llamada de streaming de servidor comienza con el envío de un mensaje de solicitud por parte del cliente.</span><span class="sxs-lookup"><span data-stu-id="78273-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="78273-166">`ResponseStream.MoveNext()` lee los mensajes transmitidos desde el servicio.</span><span class="sxs-lookup"><span data-stu-id="78273-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="78273-167">La llamada de streaming del servidor se completa cuando `ResponseStream.MoveNext()` devuelve `false`.</span><span class="sxs-lookup"><span data-stu-id="78273-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="78273-168">Al usar C# 8 o una versión posterior, se puede usar la sintaxis de `await foreach` para leer los mensajes.</span><span class="sxs-lookup"><span data-stu-id="78273-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="78273-169">El método de extensión `IAsyncStreamReader<T>.ReadAllAsync()` lee todos los mensajes de la secuencia de respuesta:</span><span class="sxs-lookup"><span data-stu-id="78273-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="78273-170">Llamada de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="78273-170">Client streaming call</span></span>

<span data-ttu-id="78273-171">Una llamada de streaming de cliente comienza *sin* el envío de un mensaje por parte del cliente.</span><span class="sxs-lookup"><span data-stu-id="78273-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="78273-172">El cliente puede elegir enviar mensajes con `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="78273-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="78273-173">Cuando el cliente ha terminado de enviar mensajes, se debe llamar a `RequestStream.CompleteAsync()` para notificar al servicio.</span><span class="sxs-lookup"><span data-stu-id="78273-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="78273-174">La llamada finaliza cuando el servicio devuelve un mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="78273-174">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="78273-175">Llamada de streaming bidireccional</span><span class="sxs-lookup"><span data-stu-id="78273-175">Bi-directional streaming call</span></span>

<span data-ttu-id="78273-176">Una llamada de streaming bidireccional comienza *sin* el envío de un mensaje por parte del cliente.</span><span class="sxs-lookup"><span data-stu-id="78273-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="78273-177">El cliente puede elegir enviar mensajes con `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="78273-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="78273-178">Se puede acceder a los mensajes transmitidos desde el servicio con `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="78273-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="78273-179">La llamada de streaming bidireccional se completa cuando `ResponseStream` no tiene más mensajes.</span><span class="sxs-lookup"><span data-stu-id="78273-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="78273-180">Para obtener el mejor rendimiento y evitar errores innecesarios en el cliente y el servicio, intente completar correctamente las llamadas de streaming bidireccionales.</span><span class="sxs-lookup"><span data-stu-id="78273-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="78273-181">Una llamada bidireccional se completa correctamente cuando tanto el servidor como el cliente han terminado de leer la secuencia de solicitud.</span><span class="sxs-lookup"><span data-stu-id="78273-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="78273-182">La llamada de ejemplo anterior es una llamada bidireccional que finaliza correctamente.</span><span class="sxs-lookup"><span data-stu-id="78273-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="78273-183">En la llamada, el cliente:</span><span class="sxs-lookup"><span data-stu-id="78273-183">In the call, the client:</span></span>

1. <span data-ttu-id="78273-184">Inicia una nueva llamada de streaming bidireccional mediante la llamada a `EchoClient.Echo`.</span><span class="sxs-lookup"><span data-stu-id="78273-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="78273-185">Crea una tarea en segundo plano para leer los mensajes del servicio mediante `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="78273-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="78273-186">Envía mensajes al servidor con `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="78273-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="78273-187">Notifica al servidor que ha finalizado el envío de mensajes con `RequestStream.CompleteAsync()`.</span><span class="sxs-lookup"><span data-stu-id="78273-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="78273-188">Espera hasta que la tarea en segundo plano haya leído todos los mensajes entrantes.</span><span class="sxs-lookup"><span data-stu-id="78273-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="78273-189">Durante una llamada de streaming bidireccional, el cliente y el servicio se pueden enviar mensajes entre sí en cualquier momento.</span><span class="sxs-lookup"><span data-stu-id="78273-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="78273-190">La mejor lógica de cliente para interactuar con una llamada bidireccional varía en función de la lógica del servicio.</span><span class="sxs-lookup"><span data-stu-id="78273-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="78273-191">Acceso a los finalizadores gRPC</span><span class="sxs-lookup"><span data-stu-id="78273-191">Access gRPC trailers</span></span>

<span data-ttu-id="78273-192">Las llamadas gRPC pueden devolver finalizadores gRPC.</span><span class="sxs-lookup"><span data-stu-id="78273-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="78273-193">Los finalizadores gRPC se usan para proporcionar metadatos de nombre y valor sobre una llamada.</span><span class="sxs-lookup"><span data-stu-id="78273-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="78273-194">Los finalizadores proporcionan una funcionalidad similar a los encabezados HTTP, pero se reciben al final de la llamada.</span><span class="sxs-lookup"><span data-stu-id="78273-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="78273-195">Los finalizadores gRPC son accesibles mediante `GetTrailers()`, que devuelve una colección de metadatos.</span><span class="sxs-lookup"><span data-stu-id="78273-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="78273-196">Los finalizadores se devuelven una vez que se completa la respuesta, por lo que debe esperar todos los mensajes de respuesta antes de acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="78273-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="78273-197">Las llamadas de streaming unarias y de cliente deben esperar `ResponseAsync` antes de llamar a `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="78273-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="78273-198">Las llamadas de streaming bidireccionales y de servidor deben terminar de esperar la secuencia de respuesta antes de llamar a `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="78273-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="78273-199">También se puede acceder a los finalizadores gRPC desde `RpcException`.</span><span class="sxs-lookup"><span data-stu-id="78273-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="78273-200">Un servicio puede devolver los finalizadores junto con un estado de gRPC que no sea correcto.</span><span class="sxs-lookup"><span data-stu-id="78273-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="78273-201">En esta situación, los finalizadores se recuperan de la excepción que genera el cliente de gRPC:</span><span class="sxs-lookup"><span data-stu-id="78273-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="78273-202">Configuración de la fecha límite</span><span class="sxs-lookup"><span data-stu-id="78273-202">Configure deadline</span></span>

<span data-ttu-id="78273-203">Se recomienda configurar una fecha límite de llamada a gRPC porque proporciona un límite superior para el tiempo durante el que se puede ejecutar una llamada.</span><span class="sxs-lookup"><span data-stu-id="78273-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="78273-204">Impide que los servicios de comportamiento incorrecto se ejecuten indefinidamente y agoten los recursos del servidor.</span><span class="sxs-lookup"><span data-stu-id="78273-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="78273-205">Las fechas límite son una herramienta útil para compilar aplicaciones confiables.</span><span class="sxs-lookup"><span data-stu-id="78273-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="78273-206">Configure `CallOptions.Deadline` para establecer una fecha límite para una llamada a gRPC:</span><span class="sxs-lookup"><span data-stu-id="78273-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="78273-207">Para obtener más información, vea <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="78273-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78273-208">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="78273-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
