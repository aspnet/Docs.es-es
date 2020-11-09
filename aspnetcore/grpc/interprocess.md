---
title: Comunicación entre procesos con gRPC
author: jamesnk
description: Obtenga información sobre cómo usar gRPC para la comunicación entre procesos.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059889"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="bbd42-103">Comunicación entre procesos con gRPC</span><span class="sxs-lookup"><span data-stu-id="bbd42-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="bbd42-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="bbd42-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="bbd42-105">Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="bbd42-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="bbd42-106">TCP se ha diseñado para comunicarse a través de una red.</span><span class="sxs-lookup"><span data-stu-id="bbd42-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="bbd42-107">La [comunicación entre procesos (IPC)](https://wikipedia.org/wiki/Inter-process_communication) es más eficaz que TCP cuando el cliente y el servicio están en el mismo equipo.</span><span class="sxs-lookup"><span data-stu-id="bbd42-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="bbd42-108">En este documento se explica cómo usar gRPC con transportes personalizados en escenarios de IPC.</span><span class="sxs-lookup"><span data-stu-id="bbd42-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="bbd42-109">Configuración del servidor</span><span class="sxs-lookup"><span data-stu-id="bbd42-109">Server configuration</span></span>

<span data-ttu-id="bbd42-110">[Kestrel](xref:fundamentals/servers/kestrel) admite transportes personalizados.</span><span class="sxs-lookup"><span data-stu-id="bbd42-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="bbd42-111">Kestrel se configura en *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="bbd42-111">Kestrel is configured in *Program.cs* :</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="bbd42-112">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="bbd42-112">The preceding example:</span></span>

* <span data-ttu-id="bbd42-113">Se configuran los puntos de conexión de Kestrel en `ConfigureKestrel`.</span><span class="sxs-lookup"><span data-stu-id="bbd42-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="bbd42-114">Se llama a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escuchar un [socket de dominio Unix (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) con la ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="bbd42-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="bbd42-115">Kestrel tiene compatibilidad integrada con los puntos de conexión de UDS.</span><span class="sxs-lookup"><span data-stu-id="bbd42-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="bbd42-116">UDS se admite en Linux, macOS y las [versiones modernas de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="bbd42-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="bbd42-117">Configuración de cliente</span><span class="sxs-lookup"><span data-stu-id="bbd42-117">Client configuration</span></span>

<span data-ttu-id="bbd42-118">`GrpcChannel` admite la realización de llamadas gRPC a través de transportes personalizados.</span><span class="sxs-lookup"><span data-stu-id="bbd42-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="bbd42-119">Cuando se crea un canal, se puede configurar con un elemento `SocketsHttpHandler` que tenga un elemento `ConnectCallback` personalizado.</span><span class="sxs-lookup"><span data-stu-id="bbd42-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="bbd42-120">La devolución de llamada permite al cliente realizar conexiones a través de transportes personalizados y, después, enviar solicitudes HTTP a través de ese transporte.</span><span class="sxs-lookup"><span data-stu-id="bbd42-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bbd42-121">`SocketsHttpHandler.ConnectCallback` es una nueva API de la versión candidata para lanzamiento 2 de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="bbd42-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="bbd42-122">Ejemplo de fábrica de conexiones de sockets de dominio de Unix:</span><span class="sxs-lookup"><span data-stu-id="bbd42-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="bbd42-123">Uso del generador de conexiones personalizadas para crear un canal:</span><span class="sxs-lookup"><span data-stu-id="bbd42-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="bbd42-124">Los canales creados con el código anterior envían llamadas gRPC a través de sockets de dominio de Unix.</span><span class="sxs-lookup"><span data-stu-id="bbd42-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="bbd42-125">Se puede implementar la compatibilidad con otras tecnologías IPC por medio de la extensibilidad en Kestrel y `SocketsHttpHandler`.</span><span class="sxs-lookup"><span data-stu-id="bbd42-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
