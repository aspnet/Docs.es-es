---
title: Creación de API web JSON desde gRPC
author: jamesnk
description: Obtenga información sobre cómo crear API HTTP JSON para servicios gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/httpapi
ms.openlocfilehash: 45e2a1a5e6a9f00294147db769454b78c5b866e5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059941"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="35782-103">Creación de API web JSON desde gRPC</span><span class="sxs-lookup"><span data-stu-id="35782-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="35782-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="35782-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="35782-105">La API HTTP de gRPC es un proyecto experimental, no un producto confirmado.</span><span class="sxs-lookup"><span data-stu-id="35782-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="35782-106">Queremos:</span><span class="sxs-lookup"><span data-stu-id="35782-106">We want to:</span></span>
>
> * <span data-ttu-id="35782-107">Comprobar que nuestro enfoque para crear API web JSON para servicios gRPC funciona.</span><span class="sxs-lookup"><span data-stu-id="35782-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="35782-108">Recibir comentarios sobre si este enfoque es útil para los desarrolladores de .NET.</span><span class="sxs-lookup"><span data-stu-id="35782-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="35782-109">[Deje sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167) para asegurarnos de que creamos algo que gusta a los desarrolladores y los hace productivos.</span><span class="sxs-lookup"><span data-stu-id="35782-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="35782-110">gRPC es una forma moderna de comunicarse entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="35782-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="35782-111">gRPC usa HTTP/2, streaming, Protobuf y contratos de mensaje para crear servicios en tiempo real de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="35782-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="35782-112">Una limitación de gRPC es que no se puede usar en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="35782-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="35782-113">Los exploradores no son totalmente compatibles con HTTP/2, por lo que REST y JSON son la manera principal de obtener datos en aplicaciones de explorador.</span><span class="sxs-lookup"><span data-stu-id="35782-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="35782-114">Incluso con las ventajas que aporta gRPC, REST y JSON ocupan un lugar importante en las aplicaciones modernas.</span><span class="sxs-lookup"><span data-stu-id="35782-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="35782-115">La compilación de API web JSON \* **y** _ gRPC agrega una sobrecarga no deseada al desarrollo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="35782-115">Building gRPC \* **and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="35782-116">En este documento se explica cómo crear API web JSON con servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="35782-117">API HTTP de gRPC</span><span class="sxs-lookup"><span data-stu-id="35782-117">gRPC HTTP API</span></span>

<span data-ttu-id="35782-118">La API HTTP de gRPC es una extensión experimental para ASP.NET Core que crea API JSON de RESTful para servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="35782-119">Una vez configurada, la API HTTP de gRPC permite que las aplicaciones llamen a servicios gRPC con conceptos conocidos de HTTP:</span><span class="sxs-lookup"><span data-stu-id="35782-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="35782-120">_ Verbos HTTP</span><span class="sxs-lookup"><span data-stu-id="35782-120">_ HTTP verbs</span></span>
* <span data-ttu-id="35782-121">Enlace de parámetros de dirección URL</span><span class="sxs-lookup"><span data-stu-id="35782-121">URL parameter binding</span></span>
* <span data-ttu-id="35782-122">Solicitudes y respuestas JSON</span><span class="sxs-lookup"><span data-stu-id="35782-122">JSON requests/responses</span></span>

<span data-ttu-id="35782-123">gRPC también se puede usar para llamar a servicios.</span><span class="sxs-lookup"><span data-stu-id="35782-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="35782-124">Uso</span><span class="sxs-lookup"><span data-stu-id="35782-124">Usage</span></span>

1. <span data-ttu-id="35782-125">Agregue una referencia de paquete a [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="35782-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="35782-126">Registre los servicios en *Startup.cs* con `AddGrpcHttpApi`.</span><span class="sxs-lookup"><span data-stu-id="35782-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="35782-127">Agregue los archivos [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) y [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="35782-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="35782-128">Anote los métodos gRPC en los archivos *.proto* con enlaces y rutas HTTP:</span><span class="sxs-lookup"><span data-stu-id="35782-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="35782-129">El método gRPC `SayHello` ya se puede invocar como gRPC+protobuf y como una API HTTP:</span><span class="sxs-lookup"><span data-stu-id="35782-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="35782-130">Solicitud: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="35782-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="35782-131">Respuesta: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="35782-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="35782-132">Los registros de servidor muestran que un servicio gRPC ejecuta la llamada HTTP.</span><span class="sxs-lookup"><span data-stu-id="35782-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="35782-133">La API HTTP de gRPC asigna la solicitud HTTP entrante a un mensaje de gRPC y, luego, convierte el mensaje de respuesta en JSON.</span><span class="sxs-lookup"><span data-stu-id="35782-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="35782-134">Este es un ejemplo básico.</span><span class="sxs-lookup"><span data-stu-id="35782-134">This is a basic example.</span></span> <span data-ttu-id="35782-135">Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para conocer más opciones de personalización.</span><span class="sxs-lookup"><span data-stu-id="35782-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="35782-136">API HTTP de gRPC frente a gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="35782-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="35782-137">Tanto la API HTTP de gRPC como gRPC-Web permiten llamar a servicios gRPC desde un explorador,</span><span class="sxs-lookup"><span data-stu-id="35782-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="35782-138">aunque de manera diferente:</span><span class="sxs-lookup"><span data-stu-id="35782-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="35782-139">gRPC-Web permite a las aplicaciones de explorador llamar a servicios gRPC desde el explorador con el cliente gRPC-Web y Protobuf.</span><span class="sxs-lookup"><span data-stu-id="35782-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="35782-140">gRPC-Web requiere que la aplicación de explorador genere un cliente gRPC y tiene la ventaja de que envía mensajes de Protobuf pequeños y rápidos.</span><span class="sxs-lookup"><span data-stu-id="35782-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="35782-141">La API HTTP de gRPC permite a las aplicaciones de explorador llamar a servicios gRPC como si fueran API de RESTful con JSON.</span><span class="sxs-lookup"><span data-stu-id="35782-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="35782-142">No es necesario que la aplicación de explorador genere un cliente gRPC o que disponga de información sobre gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="35782-143">No se crea ningún cliente generado para la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="35782-144">Se puede llamar al servicio `Greeter` anterior mediante las API de JavaScript del explorador:</span><span class="sxs-lookup"><span data-stu-id="35782-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="35782-145">Estado experimental</span><span class="sxs-lookup"><span data-stu-id="35782-145">Experimental status</span></span>

<span data-ttu-id="35782-146">La API HTTP de gRPC es un experimento.</span><span class="sxs-lookup"><span data-stu-id="35782-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="35782-147">Todavía no está completa y no se admite.</span><span class="sxs-lookup"><span data-stu-id="35782-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="35782-148">Nos interesa esta tecnología y las capacidades que ofrece a los desarrolladores de aplicaciones para crear rápidamente servicios gRPC y JSON al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="35782-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="35782-149">No hay ningún compromiso para completar la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="35782-150">Queremos valorar el interés que tienen los desarrolladores por la API HTTP de gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="35782-151">Si le atrae la API HTTP de gRPC, [envíenos sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="35782-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="35782-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="35782-152">grpc-gateway</span></span>

<span data-ttu-id="35782-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) es otra tecnología para crear API JSON de RESTful desde servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="35782-154">Usa las mismas anotaciones *.proto* para asignar conceptos HTTP a servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="35782-155">La principal diferencia entre grpc-gateway y la API HTTP de gRPC es que grpc-gateway usa la generación de código para crear un servidor proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="35782-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="35782-156">El proxy inverso traslada las llamadas de RESTful a gRPC y, luego, las envía al servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="35782-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="35782-157">Para obtener información sobre la instalación y el uso de grpc-gateway, consulte la [documentación de grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="35782-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35782-158">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="35782-158">Additional resources</span></span>

* [<span data-ttu-id="35782-159">Documentación de google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="35782-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
