---
title: Introducción a gRPC en .NET Core
author: juntaoluo
description: Obtenga información sobre los servicios gRPC con el servidor de Kestrel y la pila de ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 4a2f86d906c7b268b1fb2a63c50a1b8f0dd2b154
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633193"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introducción a gRPC en .NET Core

Por [John Luo](https://github.com/juntaoluo) y [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) es un marco de llamada a procedimiento remoto (RPC) de alto rendimiento e independiente del idioma.

Las principales ventajas de gRPC son:
* Marco de RPC moderno, ligero y de alto rendimiento.
* Desarrollo de la API de primer contrato utilizando búferes de protocolo de forma predeterminada, lo que permite realizar implementaciones independientes del idioma.
* Dispone de herramientas para muchos idioma con la finalidad de generar clientes y servidores fuertemente tipados.
* Admite llamadas de transmisión en secuencias bidireccionales, de servidor y de cliente.
* Uso reducido de red con serialización binaria Protobuf.

Estas ventajas hacen que gRPC sea ideal para:
* Microservicios ligeros en los que la eficiencia sea fundamental.
* Sistemas políglotas en los que se requieran varios idiomas para el desarrollo.
* Servicios en tiempo real de punto a punto que necesitan controlar respuestas o solicitudes de transmisión en secuencias.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>Compatibilidad de herramientas de C# con archivos .proto

gRPC usa un enfoque de contrato primero para el desarrollo de API. Los servicios y los mensajes se definen en los archivos *\*.proto*:

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

Los tipos .NET para los servicios, los clientes y los mensajes se generan automáticamente mediante la inclusión de archivos *\*.proto* en un proyecto:

* Agregue una referencia de paquete al paquete [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).
* Agregue archivos *\*.proto* al grupo de elementos `<Protobuf>`.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Para más información sobre la compatibilidad con las herramientas de gRPC, vea <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>Servicios gRPC en ASP.NET Core

Los servicios gRPC se puede hospedar en ASP.NET Core. Los servicios tienen una integración completa con características de ASP.NET Core populares como el registro, la inserción de dependencias (DI), la autenticación y la autorización.

La plantilla de proyecto de servicios gRPC proporciona un servicio de inicio:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService` se hereda del tipo `GreeterBase`, que se genera a partir del servicio `Greeter` en el archivo *\*.proto*. El servicio se hace accesible a los clientes de *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Para más información sobre los servicios gRPC en ASP.NET Core, vea <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Llamada a servicios gRPC con un cliente .NET

Los clientes gRPC son tipos de cliente concretos que se [generan a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets). El cliente gRPC concreto tiene métodos que se convierten en el servicio gRPC en el archivo *\*.proto*.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Un cliente gRPC se crea mediante un canal, que representa una conexión de larga duración con un servicio gRPC. Un canal se puede crear mediante `GrpcChannel.ForAddress`.

Para más información sobre cómo crear clientes y llamar a métodos de servicio diferentes, vea <xref:grpc/client>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
