---
title: Procedimientos recomendados de rendimiento con gRPC
author: jamesnk
description: Conozca los procedimientos recomendados para la compilación de servicios de gRPC de alto rendimiento.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060409"
---
# <a name="performance-best-practices-with-grpc"></a>Procedimientos recomendados de rendimiento con gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

gRPC está diseñado para servicios de alto rendimiento. En este documento se explica cómo obtener el mejor rendimiento posible de gRPC.

## <a name="reuse-grpc-channels"></a>Reutilización de canales gRPC

Se debe reutilizar un canal gRPC al realizar llamadas gRPC. La reutilización de un canal permite multiplexar las llamadas a través de una conexión HTTP/2 existente.

Si se crea un nuevo canal para cada llamada gRPC, la cantidad de tiempo que tardarán dichas llamadas en completarse puede aumentar significativamente. Cada llamada requerirá varios recorridos de red de ida y vuelta entre el cliente y el servidor para crear una conexión HTTP/2:

1. Apertura de un socket
2. Establecimiento de la conexión TCP
3. Negociación de TLS
4. Inicio de la conexión HTTP/2
5. Realización de la llamada gRPC

Los canales se pueden compartir y reutilizar de forma segura entre llamadas gRPC:

* Los clientes gRPC se crean con canales. Los clientes gRPC son objetos ligeros y no es necesario que se almacenen en caché ni reutilizarlos.
* Se pueden crear varios clientes gRPC a partir de un canal, incluidos distintos tipos de clientes.
* Varios subprocesos pueden usar de forma segura un canal y los clientes creados a partir del canal.
* Los clientes creados a partir del canal pueden realizar varias llamadas simultáneas.

La fábrica de cliente de gRPC ofrece una manera centralizada de configurar canales. Reutiliza automáticamente los canales subyacentes. Para obtener más información, vea <xref:grpc/clientfactory>.

## <a name="connection-concurrency"></a>Simultaneidad de conexiones

Las conexiones HTTP/2 suelen tener un límite en el número de [flujos simultáneos máximos (solicitudes HTTP activas)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) en una conexión al mismo tiempo. De forma predeterminada, la mayoría de los servidores establecen este límite en 100 flujos simultáneos.

Un canal gRPC usa una única conexión HTTP/2 y las llamadas simultáneas se multiplexan en esa conexión. Cuando el número de llamadas activas alcanza el límite del flujo de conexiones, las llamadas adicionales se ponen en cola en el cliente. Las llamadas en cola esperan a que se completen las activas antes de enviarse. Las aplicaciones con una carga elevada o las llamadas gRPC de streaming de larga duración podrían ver las incidencias de rendimiento que provocan las llamadas en cola debido a este límite.

::: moniker range=">= aspnetcore-5.0"

.NET 5 presenta la propiedad `SocketsHttpHandler.EnableMultipleHttp2Connections`. Cuando se establece en `true`, un canal crea conexiones HTTP/2 adicionales cuando se alcanza el límite de flujos simultáneos. Cuando se crea un elemento `GrpcChannel`, su elemento `SocketsHttpHandler` interno se configura automáticamente para crear conexiones HTTP/2 adicionales. Si una aplicación configura su propio controlador, valore la posibilidad de establecer `EnableMultipleHttp2Connections` en `true`:

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

Hay un par de soluciones alternativas para las aplicaciones de .NET Core 3.1:

* Cree canales gRPC independientes para las áreas de la aplicación que tengan una carga elevada. Por ejemplo, el servicio gRPC `Logger` puede tener una carga elevada. Use un canal independiente para crear el elemento `LoggerClient` en la aplicación.
* Use un grupo de canales gRPC, por ejemplo, cree una lista de canales gRPC. `Random` se utiliza para elegir un canal de la lista cada vez que se necesita un canal gRPC. El uso de `Random` distribuye aleatoriamente llamadas en varias conexiones.

> [!IMPORTANT]
> Otra manera de resolver este problema consiste en aumentar el límite de flujos simultáneos máximos en el servidor. En Kestrel, se configura con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.
>
> No se recomienda aumentar el límite máximo de flujos simultáneos. El hecho de usar demasiados flujos en una única conexión HTTP/2 genera nuevas incidencias de rendimiento:
>
> * Se crea una contención de subprocesos entre flujos que intentan escribir en la conexión.
> * La pérdida de paquetes de conexión provoca que todas las llamadas se bloqueen en el nivel TCP.

## <a name="load-balancing"></a>Equilibrio de carga

Algunos equilibradores de carga no funcionan de forma eficaz con gRPC. Los equilibradores de carga L4 (transporte) operan en un nivel de conexión, para lo que distribuyen las conexiones TCP a través de puntos de conexión. Este enfoque funciona bien para el equilibrio de carga de llamadas API realizadas con HTTP/1.1. Las llamadas simultáneas realizadas con HTTP/1.1 se envían en conexiones diferentes, lo que permite equilibrar la carga de las llamadas entre los puntos de conexión.

Dado que los equilibradores de carga L4 operan en un nivel de conexión, no funcionan bien con gRPC. gRPC usa HTTP/2, que multiplexa varias llamadas en una sola conexión TCP. Todas las llamadas gRPC a través de esa conexión van a un punto de conexión.

Hay dos opciones para equilibrar la carga de gRPC de forma eficaz:

* Equilibrio de carga del lado cliente
* Equilibrio de carga de proxy L7 (aplicación)

> [!NOTE]
> Solo se puede equilibrar la carga de las llamadas gRPC entre los puntos de conexión. Una vez que se ha establecido una llamada gRPC de streaming, todos los mensajes enviados a través de la secuencia van a un punto de conexión.

### <a name="client-side-load-balancing"></a>Equilibrio de carga del lado cliente

Con el equilibrio de carga del lado cliente, el cliente conoce los puntos de conexión. Para cada llamada gRPC, selecciona un punto de conexión diferente al que se envía la llamada. El equilibrio de carga del lado cliente es una buena opción cuando la latencia es importante. No hay ningún proxy entre el cliente y el servicio, por lo que la llamada se envía directamente al servicio. El inconveniente del equilibrio de carga del lado cliente es que cada cliente debe realizar un seguimiento de los puntos de conexión disponibles que debe usar.

El equilibrio de carga de cliente de lista de direcciones es una técnica en la que el estado del equilibrio de carga se almacena en una ubicación central. Los clientes consultan periódicamente la ubicación central para obtener información que usarán al tomar decisiones sobre el equilibrio de carga.

`Grpc.Net.Client` actualmente no admite el equilibrio de carga del lado cliente. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) es una buena opción si se requiere el equilibrio de carga del lado cliente en .NET.

### <a name="proxy-load-balancing"></a>Equilibrio de carga de proxy

Un proxy L7 (aplicación) funciona en un nivel superior que un proxy L4 (transporte). Los proxies L7 entienden HTTP/2 y pueden distribuir llamadas gRPC multiplexadas en el proxy en una conexión HTTP/2 a través de varios puntos de conexión. El uso de un proxy es más sencillo que el equilibrio de carga del lado cliente, pero puede agregar latencia adicional a las llamadas gRPC.

Hay muchos servidores proxy L7 disponibles. Estas son algunas opciones:

* [Envoy](https://www.envoyproxy.io/): popular proxy de código abierto.
* [Linkerd](https://linkerd.io/): malla de servicio para Kubernetes.
* [YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/): proxy de código abierto en versión preliminar escrito en .NET.

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>Comunicación entre procesos

Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP. TCP es excelente para la comunicación a través de una red, pero la [comunicación entre procesos](https://wikipedia.org/wiki/Inter-process_communication) (IPC) es más eficaz cuando el cliente y el servicio están en la misma máquina.

Considere la posibilidad de usar un transporte como sockets de dominio de Unix o canalizaciones con nombre en las llamadas gRPC entre procesos en el mismo equipo. Para obtener más información, vea <xref:grpc/interprocess>.

## <a name="keep-alive-pings"></a>Pings de mantenimiento de conexión

Los pings de mantenimiento de conexión se pueden usar para mantener las conexiones HTTP/2 activas durante los períodos de inactividad. Tener una conexión HTTP/2 existente lista cuando una aplicación reanuda la actividad permite que las llamadas gRPC iniciales se realicen rápidamente, sin un retraso provocado por el restablecimiento de la conexión.

Los pings de mantenimiento de conexión se configuran en <xref:System.Net.Http.SocketsHttpHandler>:

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

El código anterior configura un canal que envía un ping de mantenimiento de conexión al servidor cada 60 segundos durante los períodos de inactividad. El ping garantiza que el servidor y los servidores proxy en uso no cerrarán la conexión debido a la inactividad.

::: moniker-end

## <a name="streaming"></a>Streaming

El streaming bidireccional de gRPC se puede usar para reemplazar las llamadas gRPC unarias en escenarios de alto rendimiento. Una vez que se ha iniciado un flujo bidireccional, los mensajes de streaming en ambas direcciones son más rápidos que el envío de mensajes con varias llamadas gRPC unarias. Los mensajes transmitidos se envían como datos en una solicitud HTTP/2 existente y eliminan la sobrecarga de crear una nueva solicitud HTTP/2 para cada llamada unaria.

Ejemplo de servicio:

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

Ejemplo de cliente:

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

Reemplazar las llamadas unarias por streaming bidireccional por motivos de rendimiento es una técnica avanzada y no es adecuada en muchas situaciones.

El uso de llamadas de streaming es una buena opción cuando ocurre lo siguiente:

1. Se requiere rendimiento alto o latencia baja.
2. gRPC y HTTP/2 se identifican como cuellos de botella de rendimiento.
3. Un rol de trabajo en el cliente está enviando o recibiendo mensajes normales con un servicio gRPC.

Tenga en cuenta la complejidad adicional y las limitaciones del uso de llamadas de streaming en lugar de unarias:

1. Un error de conexión o de servicio puede interrumpir un flujo. Se requiere lógica para reiniciar el flujo si se produce un error.
2. `RequestStream.WriteAsync` no es seguro para subprocesos múltiples. Solo se puede escribir un mensaje en un flujo a la vez. El envío de mensajes desde varios subprocesos a través de un único flujo requiere una cola de productor o consumidor como <xref:System.Threading.Channels.Channel%601> para calcular las referencias de los mensajes.
3. Un método de streaming gRPC se limita a recibir un tipo de mensaje y enviar un tipo de mensaje. Por ejemplo, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` recibe `RequestMessage` y envía `ResponseMessage`. La compatibilidad de Protobuf con mensajes desconocidos o condicionales mediante `Any` y `oneof` puede solucionar esta limitación.
