---
title: Procedimientos recomendados de rendimiento con gRPC
author: jamesnk
description: Conozca los procedimientos recomendados para la compilación de servicios de gRPC de alto rendimiento.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060409"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="e7e53-103">Procedimientos recomendados de rendimiento con gRPC</span><span class="sxs-lookup"><span data-stu-id="e7e53-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="e7e53-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e7e53-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e7e53-105">gRPC está diseñado para servicios de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="e7e53-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="e7e53-106">En este documento se explica cómo obtener el mejor rendimiento posible de gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="e7e53-107">Reutilización de canales gRPC</span><span class="sxs-lookup"><span data-stu-id="e7e53-107">Reuse gRPC channels</span></span>

<span data-ttu-id="e7e53-108">Se debe reutilizar un canal gRPC al realizar llamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="e7e53-109">La reutilización de un canal permite multiplexar las llamadas a través de una conexión HTTP/2 existente.</span><span class="sxs-lookup"><span data-stu-id="e7e53-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="e7e53-110">Si se crea un nuevo canal para cada llamada gRPC, la cantidad de tiempo que tardarán dichas llamadas en completarse puede aumentar significativamente.</span><span class="sxs-lookup"><span data-stu-id="e7e53-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="e7e53-111">Cada llamada requerirá varios recorridos de red de ida y vuelta entre el cliente y el servidor para crear una conexión HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="e7e53-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="e7e53-112">Apertura de un socket</span><span class="sxs-lookup"><span data-stu-id="e7e53-112">Opening a socket</span></span>
2. <span data-ttu-id="e7e53-113">Establecimiento de la conexión TCP</span><span class="sxs-lookup"><span data-stu-id="e7e53-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="e7e53-114">Negociación de TLS</span><span class="sxs-lookup"><span data-stu-id="e7e53-114">Negotiating TLS</span></span>
4. <span data-ttu-id="e7e53-115">Inicio de la conexión HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e7e53-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="e7e53-116">Realización de la llamada gRPC</span><span class="sxs-lookup"><span data-stu-id="e7e53-116">Making the gRPC call</span></span>

<span data-ttu-id="e7e53-117">Los canales se pueden compartir y reutilizar de forma segura entre llamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="e7e53-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="e7e53-118">Los clientes gRPC se crean con canales.</span><span class="sxs-lookup"><span data-stu-id="e7e53-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="e7e53-119">Los clientes gRPC son objetos ligeros y no es necesario que se almacenen en caché ni reutilizarlos.</span><span class="sxs-lookup"><span data-stu-id="e7e53-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="e7e53-120">Se pueden crear varios clientes gRPC a partir de un canal, incluidos distintos tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="e7e53-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="e7e53-121">Varios subprocesos pueden usar de forma segura un canal y los clientes creados a partir del canal.</span><span class="sxs-lookup"><span data-stu-id="e7e53-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="e7e53-122">Los clientes creados a partir del canal pueden realizar varias llamadas simultáneas.</span><span class="sxs-lookup"><span data-stu-id="e7e53-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="e7e53-123">La fábrica de cliente de gRPC ofrece una manera centralizada de configurar canales.</span><span class="sxs-lookup"><span data-stu-id="e7e53-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="e7e53-124">Reutiliza automáticamente los canales subyacentes.</span><span class="sxs-lookup"><span data-stu-id="e7e53-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="e7e53-125">Para obtener más información, vea <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="e7e53-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="e7e53-126">Simultaneidad de conexiones</span><span class="sxs-lookup"><span data-stu-id="e7e53-126">Connection concurrency</span></span>

<span data-ttu-id="e7e53-127">Las conexiones HTTP/2 suelen tener un límite en el número de [flujos simultáneos máximos (solicitudes HTTP activas)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) en una conexión al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="e7e53-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="e7e53-128">De forma predeterminada, la mayoría de los servidores establecen este límite en 100 flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="e7e53-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="e7e53-129">Un canal gRPC usa una única conexión HTTP/2 y las llamadas simultáneas se multiplexan en esa conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="e7e53-130">Cuando el número de llamadas activas alcanza el límite del flujo de conexiones, las llamadas adicionales se ponen en cola en el cliente.</span><span class="sxs-lookup"><span data-stu-id="e7e53-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="e7e53-131">Las llamadas en cola esperan a que se completen las activas antes de enviarse.</span><span class="sxs-lookup"><span data-stu-id="e7e53-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="e7e53-132">Las aplicaciones con una carga elevada o las llamadas gRPC de streaming de larga duración podrían ver las incidencias de rendimiento que provocan las llamadas en cola debido a este límite.</span><span class="sxs-lookup"><span data-stu-id="e7e53-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e7e53-133">.NET 5 presenta la propiedad `SocketsHttpHandler.EnableMultipleHttp2Connections`.</span><span class="sxs-lookup"><span data-stu-id="e7e53-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="e7e53-134">Cuando se establece en `true`, un canal crea conexiones HTTP/2 adicionales cuando se alcanza el límite de flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="e7e53-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="e7e53-135">Cuando se crea un elemento `GrpcChannel`, su elemento `SocketsHttpHandler` interno se configura automáticamente para crear conexiones HTTP/2 adicionales.</span><span class="sxs-lookup"><span data-stu-id="e7e53-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="e7e53-136">Si una aplicación configura su propio controlador, valore la posibilidad de establecer `EnableMultipleHttp2Connections` en `true`:</span><span class="sxs-lookup"><span data-stu-id="e7e53-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="e7e53-137">Hay un par de soluciones alternativas para las aplicaciones de .NET Core 3.1:</span><span class="sxs-lookup"><span data-stu-id="e7e53-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="e7e53-138">Cree canales gRPC independientes para las áreas de la aplicación que tengan una carga elevada.</span><span class="sxs-lookup"><span data-stu-id="e7e53-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="e7e53-139">Por ejemplo, el servicio gRPC `Logger` puede tener una carga elevada.</span><span class="sxs-lookup"><span data-stu-id="e7e53-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="e7e53-140">Use un canal independiente para crear el elemento `LoggerClient` en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e7e53-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="e7e53-141">Use un grupo de canales gRPC, por ejemplo, cree una lista de canales gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="e7e53-142">`Random` se utiliza para elegir un canal de la lista cada vez que se necesita un canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="e7e53-143">El uso de `Random` distribuye aleatoriamente llamadas en varias conexiones.</span><span class="sxs-lookup"><span data-stu-id="e7e53-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7e53-144">Otra manera de resolver este problema consiste en aumentar el límite de flujos simultáneos máximos en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e7e53-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="e7e53-145">En Kestrel, se configura con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span><span class="sxs-lookup"><span data-stu-id="e7e53-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="e7e53-146">No se recomienda aumentar el límite máximo de flujos simultáneos.</span><span class="sxs-lookup"><span data-stu-id="e7e53-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="e7e53-147">El hecho de usar demasiados flujos en una única conexión HTTP/2 genera nuevas incidencias de rendimiento:</span><span class="sxs-lookup"><span data-stu-id="e7e53-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="e7e53-148">Se crea una contención de subprocesos entre flujos que intentan escribir en la conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="e7e53-149">La pérdida de paquetes de conexión provoca que todas las llamadas se bloqueen en el nivel TCP.</span><span class="sxs-lookup"><span data-stu-id="e7e53-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="e7e53-150">Equilibrio de carga</span><span class="sxs-lookup"><span data-stu-id="e7e53-150">Load balancing</span></span>

<span data-ttu-id="e7e53-151">Algunos equilibradores de carga no funcionan de forma eficaz con gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="e7e53-152">Los equilibradores de carga L4 (transporte) operan en un nivel de conexión, para lo que distribuyen las conexiones TCP a través de puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="e7e53-153">Este enfoque funciona bien para el equilibrio de carga de llamadas API realizadas con HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e7e53-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="e7e53-154">Las llamadas simultáneas realizadas con HTTP/1.1 se envían en conexiones diferentes, lo que permite equilibrar la carga de las llamadas entre los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="e7e53-155">Dado que los equilibradores de carga L4 operan en un nivel de conexión, no funcionan bien con gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="e7e53-156">gRPC usa HTTP/2, que multiplexa varias llamadas en una sola conexión TCP.</span><span class="sxs-lookup"><span data-stu-id="e7e53-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="e7e53-157">Todas las llamadas gRPC a través de esa conexión van a un punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="e7e53-158">Hay dos opciones para equilibrar la carga de gRPC de forma eficaz:</span><span class="sxs-lookup"><span data-stu-id="e7e53-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="e7e53-159">Equilibrio de carga del lado cliente</span><span class="sxs-lookup"><span data-stu-id="e7e53-159">Client-side load balancing</span></span>
* <span data-ttu-id="e7e53-160">Equilibrio de carga de proxy L7 (aplicación)</span><span class="sxs-lookup"><span data-stu-id="e7e53-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="e7e53-161">Solo se puede equilibrar la carga de las llamadas gRPC entre los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="e7e53-162">Una vez que se ha establecido una llamada gRPC de streaming, todos los mensajes enviados a través de la secuencia van a un punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="e7e53-163">Equilibrio de carga del lado cliente</span><span class="sxs-lookup"><span data-stu-id="e7e53-163">Client-side load balancing</span></span>

<span data-ttu-id="e7e53-164">Con el equilibrio de carga del lado cliente, el cliente conoce los puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="e7e53-165">Para cada llamada gRPC, selecciona un punto de conexión diferente al que se envía la llamada.</span><span class="sxs-lookup"><span data-stu-id="e7e53-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="e7e53-166">El equilibrio de carga del lado cliente es una buena opción cuando la latencia es importante.</span><span class="sxs-lookup"><span data-stu-id="e7e53-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="e7e53-167">No hay ningún proxy entre el cliente y el servicio, por lo que la llamada se envía directamente al servicio.</span><span class="sxs-lookup"><span data-stu-id="e7e53-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="e7e53-168">El inconveniente del equilibrio de carga del lado cliente es que cada cliente debe realizar un seguimiento de los puntos de conexión disponibles que debe usar.</span><span class="sxs-lookup"><span data-stu-id="e7e53-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="e7e53-169">El equilibrio de carga de cliente de lista de direcciones es una técnica en la que el estado del equilibrio de carga se almacena en una ubicación central.</span><span class="sxs-lookup"><span data-stu-id="e7e53-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="e7e53-170">Los clientes consultan periódicamente la ubicación central para obtener información que usarán al tomar decisiones sobre el equilibrio de carga.</span><span class="sxs-lookup"><span data-stu-id="e7e53-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="e7e53-171">`Grpc.Net.Client` actualmente no admite el equilibrio de carga del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="e7e53-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="e7e53-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) es una buena opción si se requiere el equilibrio de carga del lado cliente en .NET.</span><span class="sxs-lookup"><span data-stu-id="e7e53-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="e7e53-173">Equilibrio de carga de proxy</span><span class="sxs-lookup"><span data-stu-id="e7e53-173">Proxy load balancing</span></span>

<span data-ttu-id="e7e53-174">Un proxy L7 (aplicación) funciona en un nivel superior que un proxy L4 (transporte).</span><span class="sxs-lookup"><span data-stu-id="e7e53-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="e7e53-175">Los proxies L7 entienden HTTP/2 y pueden distribuir llamadas gRPC multiplexadas en el proxy en una conexión HTTP/2 a través de varios puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="e7e53-176">El uso de un proxy es más sencillo que el equilibrio de carga del lado cliente, pero puede agregar latencia adicional a las llamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="e7e53-177">Hay muchos servidores proxy L7 disponibles.</span><span class="sxs-lookup"><span data-stu-id="e7e53-177">There are many L7 proxies available.</span></span> <span data-ttu-id="e7e53-178">Estas son algunas opciones:</span><span class="sxs-lookup"><span data-stu-id="e7e53-178">Some options are:</span></span>

* <span data-ttu-id="e7e53-179">[Envoy](https://www.envoyproxy.io/): popular proxy de código abierto.</span><span class="sxs-lookup"><span data-stu-id="e7e53-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="e7e53-180">[Linkerd](https://linkerd.io/): malla de servicio para Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="e7e53-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="e7e53-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/): proxy de código abierto en versión preliminar escrito en .NET.</span><span class="sxs-lookup"><span data-stu-id="e7e53-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="e7e53-182">Comunicación entre procesos</span><span class="sxs-lookup"><span data-stu-id="e7e53-182">Inter-process communication</span></span>

<span data-ttu-id="e7e53-183">Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="e7e53-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="e7e53-184">TCP es excelente para la comunicación a través de una red, pero la [comunicación entre procesos](https://wikipedia.org/wiki/Inter-process_communication) (IPC) es más eficaz cuando el cliente y el servicio están en la misma máquina.</span><span class="sxs-lookup"><span data-stu-id="e7e53-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="e7e53-185">Considere la posibilidad de usar un transporte como sockets de dominio de Unix o canalizaciones con nombre en las llamadas gRPC entre procesos en el mismo equipo.</span><span class="sxs-lookup"><span data-stu-id="e7e53-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="e7e53-186">Para obtener más información, vea <xref:grpc/interprocess>.</span><span class="sxs-lookup"><span data-stu-id="e7e53-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="e7e53-187">Pings de mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="e7e53-187">Keep alive pings</span></span>

<span data-ttu-id="e7e53-188">Los pings de mantenimiento de conexión se pueden usar para mantener las conexiones HTTP/2 activas durante los períodos de inactividad.</span><span class="sxs-lookup"><span data-stu-id="e7e53-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="e7e53-189">Tener una conexión HTTP/2 existente lista cuando una aplicación reanuda la actividad permite que las llamadas gRPC iniciales se realicen rápidamente, sin un retraso provocado por el restablecimiento de la conexión.</span><span class="sxs-lookup"><span data-stu-id="e7e53-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="e7e53-190">Los pings de mantenimiento de conexión se configuran en <xref:System.Net.Http.SocketsHttpHandler>:</span><span class="sxs-lookup"><span data-stu-id="e7e53-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="e7e53-191">El código anterior configura un canal que envía un ping de mantenimiento de conexión al servidor cada 60 segundos durante los períodos de inactividad.</span><span class="sxs-lookup"><span data-stu-id="e7e53-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="e7e53-192">El ping garantiza que el servidor y los servidores proxy en uso no cerrarán la conexión debido a la inactividad.</span><span class="sxs-lookup"><span data-stu-id="e7e53-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="e7e53-193">Streaming</span><span class="sxs-lookup"><span data-stu-id="e7e53-193">Streaming</span></span>

<span data-ttu-id="e7e53-194">El streaming bidireccional de gRPC se puede usar para reemplazar las llamadas gRPC unarias en escenarios de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="e7e53-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="e7e53-195">Una vez que se ha iniciado un flujo bidireccional, los mensajes de streaming en ambas direcciones son más rápidos que el envío de mensajes con varias llamadas gRPC unarias.</span><span class="sxs-lookup"><span data-stu-id="e7e53-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="e7e53-196">Los mensajes transmitidos se envían como datos en una solicitud HTTP/2 existente y eliminan la sobrecarga de crear una nueva solicitud HTTP/2 para cada llamada unaria.</span><span class="sxs-lookup"><span data-stu-id="e7e53-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="e7e53-197">Ejemplo de servicio:</span><span class="sxs-lookup"><span data-stu-id="e7e53-197">Example service:</span></span>

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

<span data-ttu-id="e7e53-198">Ejemplo de cliente:</span><span class="sxs-lookup"><span data-stu-id="e7e53-198">Example client:</span></span>

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

<span data-ttu-id="e7e53-199">Reemplazar las llamadas unarias por streaming bidireccional por motivos de rendimiento es una técnica avanzada y no es adecuada en muchas situaciones.</span><span class="sxs-lookup"><span data-stu-id="e7e53-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="e7e53-200">El uso de llamadas de streaming es una buena opción cuando ocurre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e7e53-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="e7e53-201">Se requiere rendimiento alto o latencia baja.</span><span class="sxs-lookup"><span data-stu-id="e7e53-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="e7e53-202">gRPC y HTTP/2 se identifican como cuellos de botella de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="e7e53-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="e7e53-203">Un rol de trabajo en el cliente está enviando o recibiendo mensajes normales con un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7e53-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="e7e53-204">Tenga en cuenta la complejidad adicional y las limitaciones del uso de llamadas de streaming en lugar de unarias:</span><span class="sxs-lookup"><span data-stu-id="e7e53-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="e7e53-205">Un error de conexión o de servicio puede interrumpir un flujo.</span><span class="sxs-lookup"><span data-stu-id="e7e53-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="e7e53-206">Se requiere lógica para reiniciar el flujo si se produce un error.</span><span class="sxs-lookup"><span data-stu-id="e7e53-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="e7e53-207">`RequestStream.WriteAsync` no es seguro para subprocesos múltiples.</span><span class="sxs-lookup"><span data-stu-id="e7e53-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="e7e53-208">Solo se puede escribir un mensaje en un flujo a la vez.</span><span class="sxs-lookup"><span data-stu-id="e7e53-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="e7e53-209">El envío de mensajes desde varios subprocesos a través de un único flujo requiere una cola de productor o consumidor como <xref:System.Threading.Channels.Channel%601> para calcular las referencias de los mensajes.</span><span class="sxs-lookup"><span data-stu-id="e7e53-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="e7e53-210">Un método de streaming gRPC se limita a recibir un tipo de mensaje y enviar un tipo de mensaje.</span><span class="sxs-lookup"><span data-stu-id="e7e53-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="e7e53-211">Por ejemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recibe `RequestMessage` y envía `ResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="e7e53-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="e7e53-212">La compatibilidad de Protobuf con mensajes desconocidos o condicionales mediante `Any` y `oneof` puede solucionar esta limitación.</span><span class="sxs-lookup"><span data-stu-id="e7e53-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
