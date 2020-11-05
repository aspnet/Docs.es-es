---
title: Comunicación entre procesos con gRPC
author: jamesnk
description: Obtenga información sobre cómo usar gRPC para la comunicación entre procesos.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059889"
---
# <a name="inter-process-communication-with-grpc"></a>Comunicación entre procesos con gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

Las llamadas gRPC entre un cliente y un servicio se envían normalmente a través de sockets TCP. TCP se ha diseñado para comunicarse a través de una red. La [comunicación entre procesos (IPC)](https://wikipedia.org/wiki/Inter-process_communication) es más eficaz que TCP cuando el cliente y el servicio están en el mismo equipo. En este documento se explica cómo usar gRPC con transportes personalizados en escenarios de IPC.

## <a name="server-configuration"></a>Configuración del servidor

[Kestrel](xref:fundamentals/servers/kestrel) admite transportes personalizados. Kestrel se configura en *Program.cs* :

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
* Se llama a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para escuchar un [socket de dominio Unix (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) con la ruta de acceso especificada.

Kestrel tiene compatibilidad integrada con los puntos de conexión de UDS. UDS se admite en Linux, macOS y las [versiones modernas de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuración de cliente

`GrpcChannel` admite la realización de llamadas gRPC a través de transportes personalizados. Cuando se crea un canal, se puede configurar con un elemento `SocketsHttpHandler` que tenga un elemento `ConnectCallback` personalizado. La devolución de llamada permite al cliente realizar conexiones a través de transportes personalizados y, después, enviar solicitudes HTTP a través de ese transporte.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` es una nueva API de la versión candidata para lanzamiento 2 de .NET 5.

Ejemplo de fábrica de conexiones de sockets de dominio de Unix:

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

Uso del generador de conexiones personalizadas para crear un canal:

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

Los canales creados con el código anterior envían llamadas gRPC a través de sockets de dominio de Unix. Se puede implementar la compatibilidad con otras tecnologías IPC por medio de la extensibilidad en Kestrel y `SocketsHttpHandler`.
