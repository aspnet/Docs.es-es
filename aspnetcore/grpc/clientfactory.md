---
title: Integración de la fábrica de cliente de gRPC en .NET Core
author: jamesnk
description: Aprenda a crear clientes gRPC mediante la fábrica de cliente.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060955"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>Integración de la fábrica de cliente de gRPC en .NET Core

La integración de gRPC con `HttpClientFactory` ofrece una manera centralizada de crear clientes gRPC. Se puede usar como alternativa a la [configuración de instancias de cliente de gRPC independientes](xref:grpc/client). La integración de fábrica está disponible en el paquete NuGet [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory).

La fábrica ofrece las ventajas siguientes:

* Proporciona una ubicación central para configurar instancias de cliente de gRPC.
* Administra la duración del objeto `HttpClientMessageHandler` subyacente.
* Propagación automática de la fecha límite y la cancelación en un servicio gRPC de ASP.NET Core

## <a name="register-grpc-clients"></a>Registro de clientes gRPC

Para registrar un cliente gRPC, se puede usar el método de extensión genérico `AddGrpcClient` dentro de `Startup.ConfigureServices`, y especificar la clase del cliente con tipo de gRPC y la dirección del servicio:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

El tipo de cliente gRPC se registra como transitorio con la inserción de dependencias (DI). Ahora el cliente se puede insertar y consumir directamente en los tipos creados por inserción de dependencias. Los controladores de ASP.NET Core MVC, los concentradores de SignalR y los servicios gRPC son lugares en los que se pueden insertar clientes gRPC de forma automática:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Configuración de HttpClient

`HttpClientFactory` crea el objeto `HttpClient` que usa el cliente gRPC. Se pueden usar métodos `HttpClientFactory` estándar para agregar middleware de solicitud de salida o para configurar el objeto `HttpClientHandler` subyacente de `HttpClient`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Para obtener más información, vea [Realización de solicitudes HTTP con IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configuración del canal y los interceptores

Hay métodos específicos de gRPC disponibles para:

* Configurar el canal subyacente de un cliente gRPC.
* Agregar instancias de `Interceptor` que el cliente usará al realizar llamadas a gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Propagación de la fecha límite y la cancelación

Los clientes gRPC creados por la fábrica en un servicio gRPC se pueden configurar con `EnableCallContextPropagation()` para propagar automáticamente la fecha límite y el token de cancelación a las llamadas secundarias. El método de extensión `EnableCallContextPropagation()` está disponible en el paquete NuGet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory).

La propagación del contexto de llamada funciona mediante la lectura de la fecha límite y el token de cancelación del contexto de solicitud gRPC actual y su propagación automática a las llamadas salientes realizadas por el cliente gRPC. La propagación del contexto de llamada es una excelente manera de garantizar que los escenarios complejos de gRPC anidados siempre propagan la fecha límite y la cancelación.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

De forma predeterminada, `EnableCallContextPropagation` genera un error si el cliente se usa fuera del contexto de una llamada a gRPC. El error se ha diseñado para avisarle de que no hay un contexto de llamada que propagar. Si desea utilizar el cliente fuera de un contexto de llamada, suprima el error cuando el cliente esté configurado con `SuppressContextNotFoundErrors`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Para obtener más información sobre las fechas límite y la cancelación de RPC, vea [Ciclo de vida de RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
