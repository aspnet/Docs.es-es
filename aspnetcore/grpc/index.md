---
title: Introducción a gRPC en .NET Core
author: juntaoluo
description: Obtenga información sobre los servicios gRPC con el servidor de Kestrel y la pila de ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
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
ms.openlocfilehash: d21d808181d44b4c7e800df61ec1f3accdb58539
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016107"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="3ec1d-103">Introducción a gRPC en .NET Core</span><span class="sxs-lookup"><span data-stu-id="3ec1d-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="3ec1d-104">Por [John Luo](https://github.com/juntaoluo) y [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3ec1d-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3ec1d-105">[gRPC](https://grpc.io/docs/guides/) es un marco de llamada a procedimiento remoto (RPC) de alto rendimiento e independiente del idioma.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="3ec1d-106">Las principales ventajas de gRPC son:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="3ec1d-107">Marco de RPC moderno, ligero y de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="3ec1d-108">Desarrollo de la API de primer contrato utilizando búferes de protocolo de forma predeterminada, lo que permite realizar implementaciones independientes del idioma.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="3ec1d-109">Dispone de herramientas para muchos idioma con la finalidad de generar clientes y servidores fuertemente tipados.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="3ec1d-110">Admite llamadas de transmisión en secuencias bidireccionales, de servidor y de cliente.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="3ec1d-111">Uso reducido de red con serialización binaria Protobuf.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="3ec1d-112">Estas ventajas hacen que gRPC sea ideal para:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="3ec1d-113">Microservicios ligeros en los que la eficiencia sea fundamental.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="3ec1d-114">Sistemas políglotas en los que se requieran varios idiomas para el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="3ec1d-115">Servicios en tiempo real de punto a punto que necesitan controlar respuestas o solicitudes de transmisión en secuencias.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="3ec1d-116">Compatibilidad de herramientas de C# con archivos .proto</span><span class="sxs-lookup"><span data-stu-id="3ec1d-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="3ec1d-117">gRPC usa un enfoque de contrato primero para el desarrollo de API.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="3ec1d-118">Los servicios y los mensajes se definen en los archivos *\*.proto*:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-118">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="3ec1d-119">Los tipos .NET para los servicios, los clientes y los mensajes se generan automáticamente mediante la inclusión de archivos *\*.proto* en un proyecto:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="3ec1d-120">Agregue una referencia de paquete al paquete [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="3ec1d-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="3ec1d-121">Agregue archivos *\*.proto* al grupo de elementos `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="3ec1d-122">Para más información sobre la compatibilidad con las herramientas de gRPC, vea <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="3ec1d-123">Servicios gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ec1d-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="3ec1d-124">Los servicios gRPC se puede hospedar en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="3ec1d-125">Los servicios tienen una integración completa con características de ASP.NET Core populares como el registro, la inserción de dependencias (DI), la autenticación y la autorización.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="3ec1d-126">La plantilla de proyecto de servicios gRPC proporciona un servicio de inicio:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-126">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="3ec1d-127">`GreeterService` se hereda del tipo `GreeterBase`, que se genera a partir del servicio `Greeter` en el archivo *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="3ec1d-128">El servicio se hace accesible a los clientes de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3ec1d-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="3ec1d-129">Para más información sobre los servicios gRPC en ASP.NET Core, vea <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="3ec1d-130">Llamada a servicios gRPC con un cliente .NET</span><span class="sxs-lookup"><span data-stu-id="3ec1d-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="3ec1d-131">Los clientes gRPC son tipos de cliente concretos que se [generan a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="3ec1d-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="3ec1d-132">El cliente gRPC concreto tiene métodos que se convierten en el servicio gRPC en el archivo *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="3ec1d-133">Un cliente gRPC se crea mediante un canal, que representa una conexión de larga duración con un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="3ec1d-134">Un canal se puede crear mediante `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="3ec1d-135">Para más información sobre cómo crear clientes y llamar a métodos de servicio diferentes, vea <xref:grpc/client>.</span><span class="sxs-lookup"><span data-stu-id="3ec1d-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ec1d-136">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3ec1d-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
