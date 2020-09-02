---
title: Comunicación entre procesos con gRPC
author: jamesnk
description: Obtenga información sobre cómo usar gRPC para la comunicación entre procesos.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945581"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="1abd7-103">Comunicación entre procesos con gRPC</span><span class="sxs-lookup"><span data-stu-id="1abd7-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="1abd7-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1abd7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1abd7-105">Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="1abd7-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="1abd7-106">TCP es excelente para la comunicación a través de una red, pero la [comunicación entre procesos](https://wikipedia.org/wiki/Inter-process_communication) (IPC) es más eficaz cuando el cliente y el servicio están en la misma máquina.</span><span class="sxs-lookup"><span data-stu-id="1abd7-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="1abd7-107">En este documento se explica cómo usar gRPC con transportes personalizados en escenarios de IPC.</span><span class="sxs-lookup"><span data-stu-id="1abd7-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="1abd7-108">Configuración del servidor</span><span class="sxs-lookup"><span data-stu-id="1abd7-108">Server configuration</span></span>

<span data-ttu-id="1abd7-109">Los transportes personalizados son compatibles con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1abd7-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="1abd7-110">Kestrel se configura en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1abd7-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="1abd7-111">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="1abd7-111">The preceding example:</span></span>

* <span data-ttu-id="1abd7-112">Se configuran los puntos de conexión de Kestrel en `ConfigureKestrel`.</span><span class="sxs-lookup"><span data-stu-id="1abd7-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="1abd7-113">Se llama a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escuchar un [socket de dominio Unix (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) con la ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="1abd7-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="1abd7-114">Kestrel tiene compatibilidad integrada con los puntos de conexión de UDS.</span><span class="sxs-lookup"><span data-stu-id="1abd7-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="1abd7-115">UDS se admite en Linux, macOS y las [versiones modernas de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="1abd7-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="1abd7-116">Configuración de cliente</span><span class="sxs-lookup"><span data-stu-id="1abd7-116">Client configuration</span></span>

<span data-ttu-id="1abd7-117">`GrpcChannel` admite la realización de llamadas gRPC a través de transportes personalizados.</span><span class="sxs-lookup"><span data-stu-id="1abd7-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="1abd7-118">Cuando se crea un canal, se puede configurar con un elemento `SocketsHttpHandler` que tenga un elemento `ConnectionFactory` personalizado.</span><span class="sxs-lookup"><span data-stu-id="1abd7-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="1abd7-119">La fábrica permite al cliente realizar conexiones a través de transportes personalizados y, después, enviar solicitudes HTTP a través de ese transporte.</span><span class="sxs-lookup"><span data-stu-id="1abd7-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1abd7-120">`ConnectionFactory` es una nueva API de la versión candidata para lanzamiento 1 de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="1abd7-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="1abd7-121">Ejemplo de fábrica de conexiones de sockets de dominio de Unix:</span><span class="sxs-lookup"><span data-stu-id="1abd7-121">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

<span data-ttu-id="1abd7-122">Uso del generador de conexiones personalizadas para crear un canal:</span><span class="sxs-lookup"><span data-stu-id="1abd7-122">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="1abd7-123">Los canales creados con el código anterior envían llamadas gRPC a través de sockets de dominio de Unix.</span><span class="sxs-lookup"><span data-stu-id="1abd7-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
