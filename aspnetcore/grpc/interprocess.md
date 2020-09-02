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
# <a name="inter-process-communication-with-grpc"></a>Comunicación entre procesos con gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP. TCP es excelente para la comunicación a través de una red, pero la [comunicación entre procesos](https://wikipedia.org/wiki/Inter-process_communication) (IPC) es más eficaz cuando el cliente y el servicio están en la misma máquina. En este documento se explica cómo usar gRPC con transportes personalizados en escenarios de IPC.

## <a name="server-configuration"></a>Configuración del servidor

Los transportes personalizados son compatibles con Kestrel. Kestrel se configura en *Program.cs*:

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

Ejemplo anterior:

* Se configuran los puntos de conexión de Kestrel en `ConfigureKestrel`.
* Se llama a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escuchar un [socket de dominio Unix (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) con la ruta de acceso especificada.

Kestrel tiene compatibilidad integrada con los puntos de conexión de UDS. UDS se admite en Linux, macOS y las [versiones modernas de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuración de cliente

`GrpcChannel` admite la realización de llamadas gRPC a través de transportes personalizados. Cuando se crea un canal, se puede configurar con un elemento `SocketsHttpHandler` que tenga un elemento `ConnectionFactory` personalizado. La fábrica permite al cliente realizar conexiones a través de transportes personalizados y, después, enviar solicitudes HTTP a través de ese transporte.

> [!IMPORTANT]
> `ConnectionFactory` es una nueva API de la versión candidata para lanzamiento 1 de .NET 5.

Ejemplo de fábrica de conexiones de sockets de dominio de Unix:

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

Uso del generador de conexiones personalizadas para crear un canal:

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

Los canales creados con el código anterior envían llamadas gRPC a través de sockets de dominio de Unix.
