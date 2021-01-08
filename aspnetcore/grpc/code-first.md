---
title: Clientes y servicios gRPC mediante Code First con .NET
author: jamesnk
description: Conozca los conceptos básicos a la hora de escribir gRPC mediante Code First con .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880624"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="b61da-103">Clientes y servicios gRPC mediante Code First con .NET</span><span class="sxs-lookup"><span data-stu-id="b61da-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="b61da-104">Por [James Newton-King](https://twitter.com/jamesnk) y [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="b61da-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="b61da-105">gRPC mediante Code First usa tipos .NET para definir contratos de servicio y de mensajes.</span><span class="sxs-lookup"><span data-stu-id="b61da-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="b61da-106">Code First es una buena opción cuando un sistema completo usa .NET:</span><span class="sxs-lookup"><span data-stu-id="b61da-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="b61da-107">El servidor y los clientes de .NET pueden compartir los tipos de contratos de datos y servicio de .NET.</span><span class="sxs-lookup"><span data-stu-id="b61da-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="b61da-108">Esto evita la necesidad de definir contratos en la generación de código y los archivos `.proto`.</span><span class="sxs-lookup"><span data-stu-id="b61da-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="b61da-109">Code First no se recomienda en sistemas políglotas con varios idiomas.</span><span class="sxs-lookup"><span data-stu-id="b61da-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="b61da-110">Los tipos de contratos de datos y servicio de .NET no se pueden usar con plataformas que no son de .NET.</span><span class="sxs-lookup"><span data-stu-id="b61da-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="b61da-111">Para llamar a un servicio gRPC escrito mediante Code First, otras plataformas deben crear un contrato de `.proto` que coincida con el servicio.</span><span class="sxs-lookup"><span data-stu-id="b61da-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="b61da-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="b61da-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b61da-113">Para obtener ayuda con protobuf-net.Grpc, visite el [sitio web de protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) o cree una incidencia en el [repositorio de GitHub de protobuf-net.Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="b61da-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="b61da-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) es un proyecto de la comunidad y no es compatible con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="b61da-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="b61da-115">Agrega compatibilidad con Code First a `Grpc.AspNetCore` y `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="b61da-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="b61da-116">Usa tipos de .NET anotados con atributos para definir los mensajes y servicios de gRPC de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b61da-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="b61da-117">El primer paso para crear un servicio gRPC mediante Code First es definir el contrato de código:</span><span class="sxs-lookup"><span data-stu-id="b61da-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="b61da-118">Cree un proyecto que compartirán el servidor y el cliente.</span><span class="sxs-lookup"><span data-stu-id="b61da-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="b61da-119">Agregue una referencia al paquete [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="b61da-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="b61da-120">Cree tipos de contratos de datos y de servicio.</span><span class="sxs-lookup"><span data-stu-id="b61da-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="b61da-121">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="b61da-121">The preceding code:</span></span>

* <span data-ttu-id="b61da-122">Define los mensajes `HelloRequest` y `HelloReply`.</span><span class="sxs-lookup"><span data-stu-id="b61da-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="b61da-123">Define la interfaz del contrato de `IGreeterService` con el método de gRPC `SayHelloAsync` unario.</span><span class="sxs-lookup"><span data-stu-id="b61da-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="b61da-124">El contrato de servicio se implementa en el servidor y se invoca desde el cliente.</span><span class="sxs-lookup"><span data-stu-id="b61da-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="b61da-125">Los métodos definidos en las interfaces de servicio deben coincidir con determinadas firmas, en función de cómo sean, es decir, unarios, streaming de servidor, streaming de cliente o streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="b61da-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="b61da-126">Para más información sobre la definición de contratos de servicio, vea la [documentación de introducción de protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="b61da-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="b61da-127">Creación de un servicio gRPC mediante Code First</span><span class="sxs-lookup"><span data-stu-id="b61da-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="b61da-128">Para agregar un servicio gRPC mediante Code First a una aplicación de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b61da-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="b61da-129">Agregue una referencia al paquete [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="b61da-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="b61da-130">Agregue una referencia al proyecto de contrato de código compartido.</span><span class="sxs-lookup"><span data-stu-id="b61da-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="b61da-131">Cree un archivo `GreeterService.cs` e implemente la interfaz de servicio de `IGreeterService`:</span><span class="sxs-lookup"><span data-stu-id="b61da-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="b61da-132">Actualice el archivo `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b61da-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="b61da-133">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="b61da-133">In the preceding code:</span></span>

* <span data-ttu-id="b61da-134">`AddCodeFirstGrpc` registra servicios que habilitan Code First.</span><span class="sxs-lookup"><span data-stu-id="b61da-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="b61da-135">`MapGrpcService<GreeterService>` agrega el punto de conexión de servicio de Code First.</span><span class="sxs-lookup"><span data-stu-id="b61da-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="b61da-136">Los servicios gRPC implementados con Code First y archivos `.proto` pueden coexistir en la misma aplicación.</span><span class="sxs-lookup"><span data-stu-id="b61da-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="b61da-137">Todos los servicios gRPC usan la [configuración del servicio gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="b61da-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="b61da-138">Creación de un cliente de gRPC mediante Code First</span><span class="sxs-lookup"><span data-stu-id="b61da-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="b61da-139">Un cliente de gRPC mediante Code First usa el contrato de servicio para llamar a los servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="b61da-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="b61da-140">Para llamar a un servicio gRPC mediante un cliente Code First:</span><span class="sxs-lookup"><span data-stu-id="b61da-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="b61da-141">Agregue una referencia al paquete [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="b61da-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="b61da-142">Agregue una referencia al proyecto de contrato de código compartido.</span><span class="sxs-lookup"><span data-stu-id="b61da-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="b61da-143">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="b61da-143">The preceding code:</span></span>

* <span data-ttu-id="b61da-144">Crea un canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="b61da-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="b61da-145">Crea un cliente Code First a partir del canal con el método de extensión `CreateGrpcService<IGreeterService>`.</span><span class="sxs-lookup"><span data-stu-id="b61da-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="b61da-146">Llama al servicio gRPC con `SayHelloAsync`.</span><span class="sxs-lookup"><span data-stu-id="b61da-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="b61da-147">Un cliente gRPC mediante Code First se crea a partir de un canal.</span><span class="sxs-lookup"><span data-stu-id="b61da-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="b61da-148">Al igual que un cliente normal, un cliente Code First usa su [configuración de canal](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="b61da-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b61da-149">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b61da-149">Additional resources</span></span>

* [<span data-ttu-id="b61da-150">Sitio web de protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="b61da-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="b61da-151">Repositorio de GitHub de protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="b61da-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
