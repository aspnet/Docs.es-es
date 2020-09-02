---
title: Procedimientos recomendados de rendimiento en gRPC para ASP.NET Core
author: jamesnk
description: Conozca los procedimientos recomendados para la compilación de servicios de gRPC de alto rendimiento.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876729"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="111aa-103">Procedimientos recomendados de rendimiento en gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="111aa-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="111aa-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="111aa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="111aa-105">gRPC está diseñado para servicios de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="111aa-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="111aa-106">En este documento se explica cómo obtener el mejor rendimiento posible de gRPC.</span><span class="sxs-lookup"><span data-stu-id="111aa-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="111aa-107">Reutilización de un canal</span><span class="sxs-lookup"><span data-stu-id="111aa-107">Reuse channel</span></span>

<span data-ttu-id="111aa-108">Se debe reutilizar un canal gRPC al realizar llamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="111aa-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="111aa-109">La reutilización de un canal permite multiplexar las llamadas a través de una conexión HTTP/2 existente.</span><span class="sxs-lookup"><span data-stu-id="111aa-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="111aa-110">Si se crea un nuevo canal para cada llamada gRPC, la cantidad de tiempo que tardarán dichas llamadas en completarse puede aumentar significativamente.</span><span class="sxs-lookup"><span data-stu-id="111aa-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="111aa-111">Cada llamada requerirá varios recorridos de red de ida y vuelta entre el cliente y el servidor para crear una conexión HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="111aa-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="111aa-112">Apertura de un socket</span><span class="sxs-lookup"><span data-stu-id="111aa-112">Opening a socket</span></span>
2. <span data-ttu-id="111aa-113">Establecimiento de la conexión TCP</span><span class="sxs-lookup"><span data-stu-id="111aa-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="111aa-114">Negociación de TLS</span><span class="sxs-lookup"><span data-stu-id="111aa-114">Negotiating TLS</span></span>
4. <span data-ttu-id="111aa-115">Inicio de la conexión HTTP/2</span><span class="sxs-lookup"><span data-stu-id="111aa-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="111aa-116">Realización de la llamada gRPC</span><span class="sxs-lookup"><span data-stu-id="111aa-116">Making the gRPC call</span></span>

<span data-ttu-id="111aa-117">Los canales se pueden compartir y reutilizar de forma segura entre llamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="111aa-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="111aa-118">Los clientes gRPC se crean con canales.</span><span class="sxs-lookup"><span data-stu-id="111aa-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="111aa-119">Los clientes gRPC son objetos ligeros y no es necesario que se almacenen en caché ni reutilizarlos.</span><span class="sxs-lookup"><span data-stu-id="111aa-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="111aa-120">Se pueden crear varios clientes gRPC a partir de un canal, incluidos distintos tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="111aa-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="111aa-121">Varios subprocesos pueden usar de forma segura un canal y los clientes creados a partir del canal.</span><span class="sxs-lookup"><span data-stu-id="111aa-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="111aa-122">Los clientes creados a partir del canal pueden realizar varias llamadas simultáneas.</span><span class="sxs-lookup"><span data-stu-id="111aa-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="111aa-123">Simultaneidad de conexiones</span><span class="sxs-lookup"><span data-stu-id="111aa-123">Connection concurrency</span></span>

<span data-ttu-id="111aa-124">Las conexiones HTTP/2 suelen tener un límite en el número de [flujos simultáneos máximos (solicitudes HTTP activas)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) en una conexión al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="111aa-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="111aa-125">De forma predeterminada, la mayoría de los servidores establecen este límite en 100 flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="111aa-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="111aa-126">Un canal gRPC usa una única conexión HTTP/2 y las llamadas simultáneas se multiplexan en esa conexión.</span><span class="sxs-lookup"><span data-stu-id="111aa-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="111aa-127">Cuando el número de llamadas activas alcanza el límite del flujo de conexiones, las llamadas adicionales se ponen en cola en el cliente.</span><span class="sxs-lookup"><span data-stu-id="111aa-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="111aa-128">Las llamadas en cola esperan a que se completen las activas antes de enviarse.</span><span class="sxs-lookup"><span data-stu-id="111aa-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="111aa-129">Las aplicaciones con una carga elevada o las llamadas gRPC de streaming de larga duración podrían ver las incidencias de rendimiento que provocan las llamadas en cola debido a este límite.</span><span class="sxs-lookup"><span data-stu-id="111aa-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="111aa-130">.NET 5 presenta la propiedad `SocketsHttpHandler.EnableMultipleHttp2Connections`.</span><span class="sxs-lookup"><span data-stu-id="111aa-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="111aa-131">Cuando se establece en `true`, un canal crea conexiones HTTP/2 adicionales cuando se alcanza el límite de flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="111aa-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="111aa-132">Cuando se crea un elemento `GrpcChannel`, su elemento `SocketsHttpHandler` interno se configura automáticamente para crear conexiones HTTP/2 adicionales.</span><span class="sxs-lookup"><span data-stu-id="111aa-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="111aa-133">Si una aplicación configura su propio controlador, valore la posibilidad de establecer `EnableMultipleHttp2Connections` en `true`:</span><span class="sxs-lookup"><span data-stu-id="111aa-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="111aa-134">Hay un par de soluciones alternativas para las aplicaciones de .NET Core 3.1:</span><span class="sxs-lookup"><span data-stu-id="111aa-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="111aa-135">Cree canales gRPC independientes para las áreas de la aplicación que tengan una carga elevada.</span><span class="sxs-lookup"><span data-stu-id="111aa-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="111aa-136">Por ejemplo, el servicio gRPC `Logger` puede tener una carga elevada.</span><span class="sxs-lookup"><span data-stu-id="111aa-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="111aa-137">Use un canal independiente para crear el elemento `LoggerClient` en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="111aa-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="111aa-138">Use un grupo de canales gRPC, por ejemplo, cree una lista de canales gRPC.</span><span class="sxs-lookup"><span data-stu-id="111aa-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="111aa-139">`Random` se utiliza para elegir un canal de la lista cada vez que se necesita un canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="111aa-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="111aa-140">El uso de `Random` distribuye aleatoriamente llamadas en varias conexiones.</span><span class="sxs-lookup"><span data-stu-id="111aa-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="111aa-141">Otra manera de resolver este problema consiste en aumentar el límite de flujos simultáneos máximos en el servidor.</span><span class="sxs-lookup"><span data-stu-id="111aa-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="111aa-142">En Kestrel, se configura con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span><span class="sxs-lookup"><span data-stu-id="111aa-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="111aa-143">No se recomienda aumentar el límite máximo de flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="111aa-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="111aa-144">El hecho de usar demasiados flujos en una única conexión HTTP/2 genera nuevas incidencias de rendimiento:</span><span class="sxs-lookup"><span data-stu-id="111aa-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="111aa-145">Se crea una contención de subprocesos entre flujos que intentan escribir en la conexión.</span><span class="sxs-lookup"><span data-stu-id="111aa-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="111aa-146">La pérdida de paquetes de conexión provoca que todas las llamadas se bloqueen en el nivel TCP.</span><span class="sxs-lookup"><span data-stu-id="111aa-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="111aa-147">Pings de mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="111aa-147">Keep alive pings</span></span>

<span data-ttu-id="111aa-148">Los pings de mantenimiento de conexión se pueden usar para mantener las conexiones HTTP/2 activas durante los períodos de inactividad.</span><span class="sxs-lookup"><span data-stu-id="111aa-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="111aa-149">Tener una conexión HTTP/2 existente lista cuando una aplicación reanuda la actividad permite que las llamadas gRPC iniciales se realicen rápidamente, sin un retraso provocado por el restablecimiento de la conexión.</span><span class="sxs-lookup"><span data-stu-id="111aa-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="111aa-150">Los pings de mantenimiento de conexión se configuran en <xref:System.Net.Http.SocketsHttpHandler>:</span><span class="sxs-lookup"><span data-stu-id="111aa-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="111aa-151">El código anterior configura un canal que envía un ping de mantenimiento de conexión al servidor cada 60 segundos durante los períodos de inactividad.</span><span class="sxs-lookup"><span data-stu-id="111aa-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="111aa-152">El ping garantiza que el servidor y los servidores proxy en uso no cerrarán la conexión debido a la inactividad.</span><span class="sxs-lookup"><span data-stu-id="111aa-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="111aa-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="111aa-153">Streaming</span></span>

<span data-ttu-id="111aa-154">El streaming bidireccional de gRPC se puede usar para reemplazar las llamadas gRPC unarias en escenarios de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="111aa-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="111aa-155">Una vez que se ha iniciado un flujo bidireccional, los mensajes de streaming en ambas direcciones son más rápidos que el envío de mensajes con varias llamadas gRPC unarias.</span><span class="sxs-lookup"><span data-stu-id="111aa-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="111aa-156">Los mensajes transmitidos se envían como datos en una solicitud HTTP/2 existente y eliminan la sobrecarga de crear una nueva solicitud HTTP/2 para cada llamada unaria.</span><span class="sxs-lookup"><span data-stu-id="111aa-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="111aa-157">Ejemplo de servicio:</span><span class="sxs-lookup"><span data-stu-id="111aa-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="111aa-158">Ejemplo de cliente:</span><span class="sxs-lookup"><span data-stu-id="111aa-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="111aa-159">Reemplazar las llamadas unarias por streaming bidireccional por motivos de rendimiento es una técnica avanzada y no es adecuada en muchas situaciones.</span><span class="sxs-lookup"><span data-stu-id="111aa-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="111aa-160">El uso de llamadas de streaming es una buena opción cuando ocurre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="111aa-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="111aa-161">Se requiere rendimiento alto o latencia baja.</span><span class="sxs-lookup"><span data-stu-id="111aa-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="111aa-162">gRPC y HTTP/2 se identifican como cuellos de botella de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="111aa-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="111aa-163">Un rol de trabajo en el cliente está enviando o recibiendo mensajes normales con un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="111aa-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="111aa-164">Tenga en cuenta la complejidad adicional y las limitaciones del uso de llamadas de streaming en lugar de unarias:</span><span class="sxs-lookup"><span data-stu-id="111aa-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="111aa-165">Un error de conexión o de servicio puede interrumpir un flujo.</span><span class="sxs-lookup"><span data-stu-id="111aa-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="111aa-166">Se requiere lógica para reiniciar el flujo si se produce un error.</span><span class="sxs-lookup"><span data-stu-id="111aa-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="111aa-167">`RequestStream.WriteAsync` no es seguro para subprocesos múltiples.</span><span class="sxs-lookup"><span data-stu-id="111aa-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="111aa-168">Solo se puede escribir un mensaje en un flujo a la vez.</span><span class="sxs-lookup"><span data-stu-id="111aa-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="111aa-169">El envío de mensajes desde varios subprocesos a través de un único flujo requiere una cola de productor o consumidor como <xref:System.Threading.Channels.Channel%601> para calcular las referencias de los mensajes.</span><span class="sxs-lookup"><span data-stu-id="111aa-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="111aa-170">Un método de streaming gRPC se limita a recibir un tipo de mensaje y enviar un tipo de mensaje.</span><span class="sxs-lookup"><span data-stu-id="111aa-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="111aa-171">Por ejemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recibe `RequestMessage` y envía `ResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="111aa-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="111aa-172">La compatibilidad de Protobuf con mensajes desconocidos o condicionales mediante `Any` y `oneof` puede solucionar esta limitación.</span><span class="sxs-lookup"><span data-stu-id="111aa-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
